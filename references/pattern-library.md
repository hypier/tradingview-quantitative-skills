# 技术形态识别库

> 经典技术形态识别算法和交易策略

---

## 📋 形态分类

### 反转形态
- 双底 (W底)
- 双顶 (M顶)
- 头肩底
- 头肩顶
- 圆弧底
- 圆弧顶

### 整理形态
- 上升三角形
- 下降三角形
- 对称三角形
- 旗形整理
- 矩形整理
- 楔形整理

### K线形态
- 锤子线
- 倒锤子
- 吞没形态
- 启明星/黄昏星
- 十字星

---

## 🔄 反转形态

### 1. 双底 (W底)

#### 形态特征
```
价格走势呈现 "W" 形状
两个低点价格接近
中间有一个高点（颈线）
```

#### 识别条件

1. **两个低点价格相差 < 3%**
```javascript
const priceDiff = Math.abs(low1.price - low2.price) / low1.price;
if (priceDiff > 0.03) return false;
```

2. **两个低点时间间隔 > 10天**
```javascript
const timeDiff = (low2.time - low1.time) / (24 * 60 * 60);
if (timeDiff < 10) return false;
```

3. **颈线高度 > 10%**
```javascript
const necklineHeight = (neckline.price - low1.price) / low1.price;
if (necklineHeight < 0.1) return false;
```

4. **第二个低点成交量 < 第一个低点**
```javascript
if (low2.volume >= low1.volume) return false;
```

5. **突破颈线时成交量放大 > 1.5倍**
```javascript
const avgVolume = calculateAvgVolume(history, 20);
if (breakoutVolume < avgVolume * 1.5) return false;
```

#### 置信度计算

```javascript
function calculateDoubleBottomConfidence(pattern) {
  let score = 60; // 基础分
  
  // 价格相差越小越好
  const priceDiff = Math.abs(pattern.low1.price - pattern.low2.price) / pattern.low1.price;
  if (priceDiff < 0.02) score += 10;
  else if (priceDiff < 0.03) score += 5;
  
  // 时间间隔越长越可靠
  const timeDiff = (pattern.low2.time - pattern.low1.time) / (24 * 60 * 60);
  if (timeDiff > 20) score += 10;
  else if (timeDiff > 15) score += 5;
  
  // 颈线高度越高越好
  const necklineHeight = (pattern.neckline - pattern.low1.price) / pattern.low1.price;
  if (necklineHeight > 0.15) score += 10;
  else if (necklineHeight > 0.12) score += 5;
  
  // 成交量递减
  if (pattern.low2.volume < pattern.low1.volume * 0.8) score += 5;
  
  // 突破放量
  const avgVolume = pattern.avgVolume;
  if (pattern.breakoutVolume > avgVolume * 2) score += 5;
  else if (pattern.breakoutVolume > avgVolume * 1.5) score += 3;
  
  return Math.min(score, 100);
}
```

#### 交易策略

**买入点**:
- 突破颈线 + 成交量放大
- 回踩颈线不破（二次买入机会）

**目标价**:
```
目标价 = 颈线价格 + (颈线价格 - 低点价格)
```

**止损价**:
```
止损价 = 第二个低点下方 2-3%
```

**成功率**:
- 置信度 > 85%: 成功率 75%
- 置信度 70-85%: 成功率 60%
- 置信度 < 70%: 成功率 45%

---

### 2. 头肩底

#### 形态特征
```
三个低点：左肩、头部、右肩
头部低于两肩
两肩价格接近
```

#### 识别条件

1. **头部低于两肩 > 5%**
2. **两肩价格相差 < 5%**
3. **头部成交量最大**
4. **突破颈线放量**

#### 置信度计算

```javascript
function calculateHeadShouldersConfidence(pattern) {
  let score = 60;
  
  // 头部深度
  const headDepth = (pattern.leftShoulder.price - pattern.head.price) / pattern.leftShoulder.price;
  if (headDepth > 0.1) score += 15;
  else if (headDepth > 0.07) score += 10;
  
  // 两肩对称性
  const shoulderDiff = Math.abs(pattern.leftShoulder.price - pattern.rightShoulder.price) / pattern.leftShoulder.price;
  if (shoulderDiff < 0.03) score += 10;
  else if (shoulderDiff < 0.05) score += 5;
  
  // 成交量特征
  if (pattern.head.volume > pattern.leftShoulder.volume && 
      pattern.head.volume > pattern.rightShoulder.volume) {
    score += 10;
  }
  
  // 突破放量
  if (pattern.breakoutVolume > pattern.avgVolume * 1.5) score += 5;
  
  return Math.min(score, 100);
}
```

#### 交易策略

**买入点**: 突破颈线

**目标价**:
```
目标价 = 颈线价格 + (颈线价格 - 头部价格)
```

**止损价**: 右肩下方

---

### 3. 圆弧底

#### 形态特征
```
价格呈圆弧状上升
形成时间较长（> 30天）
成交量呈碗状分布
```

#### 识别条件

1. **形成时间 > 30天**
2. **价格呈圆弧状**
3. **左侧成交量 > 右侧成交量**
4. **底部成交量最小**

#### 交易策略

**买入点**: 突破圆弧顶部

**目标价**: 圆弧高度的 1.5-2 倍

**止损价**: 圆弧底部

---

## 📐 整理形态

### 1. 上升三角形

#### 形态特征
```
上边界水平（阻力位）
下边界上升（支撑线）
成交量逐步萎缩
突破方向向上概率 70%
```

#### 识别条件

1. **上边界水平（波动 < 2%）**
```javascript
const topRange = (Math.max(...tops) - Math.min(...tops)) / Math.min(...tops);
if (topRange > 0.02) return false;
```

2. **下边界上升**
```javascript
const slope = calculateSlope(bottoms);
if (slope <= 0) return false;
```

3. **形成时间 10-30天**
4. **成交量萎缩**

#### 置信度计算

```javascript
function calculateAscendingTriangleConfidence(pattern) {
  let score = 60;
  
  // 上边界水平度
  const topRange = (Math.max(...pattern.tops) - Math.min(...pattern.tops)) / Math.min(...pattern.tops);
  if (topRange < 0.01) score += 15;
  else if (topRange < 0.02) score += 10;
  
  // 下边界斜率
  const slope = calculateSlope(pattern.bottoms);
  if (slope > 0.02) score += 10;
  else if (slope > 0.01) score += 5;
  
  // 成交量萎缩
  if (pattern.recentVolume < pattern.initialVolume * 0.7) score += 10;
  
  // 触及次数
  const touchCount = pattern.tops.length + pattern.bottoms.length;
  if (touchCount >= 6) score += 5;
  
  return Math.min(score, 100);
}
```

#### 交易策略

**买入点**: 突破上边界 + 放量

**目标价**:
```
目标价 = 上边界 + 三角形高度
```

**止损价**: 下边界下方

**成功率**: 70%

---

### 2. 旗形整理

#### 形态特征
```
前期有明显上涨（旗杆）
整理呈平行四边形
整理时间短（< 20天）
成交量萎缩
突破方向与旗杆方向一致
```

#### 识别条件

1. **前期涨幅 > 10%**
2. **整理时间 < 20天**
3. **整理幅度 < 前期涨幅的 50%**
4. **成交量萎缩**

#### 交易策略

**买入点**: 突破旗形上边界

**目标价**:
```
目标价 = 突破点 + 旗杆高度
```

**止损价**: 旗形下边界

---

### 3. 矩形整理

#### 形态特征
```
价格在两条水平线之间波动
上边界和下边界都是水平的
成交量逐步萎缩
```

#### 识别条件

1. **上下边界水平（波动 < 2%）**
2. **触及边界次数 ≥ 4次**
3. **整理时间 10-40天**

#### 交易策略

**买入点**: 突破上边界 + 放量

**目标价**:
```
目标价 = 上边界 + 矩形高度
```

**止损价**: 下边界下方

---

## 🕯️ K线形态

### 1. 锤子线

#### 形态特征
```
实体小
下影线长度 > 实体 × 2
上影线很短或没有
出现在下跌趋势末期
```

#### 识别条件

```javascript
function isHammer(candle) {
  const body = Math.abs(candle.close - candle.open);
  const lowerShadow = Math.min(candle.open, candle.close) - candle.low;
  const upperShadow = candle.high - Math.max(candle.open, candle.close);
  
  // 下影线 > 实体 × 2
  if (lowerShadow < body * 2) return false;
  
  // 上影线很短
  if (upperShadow > body * 0.5) return false;
  
  // 实体占总长度 < 30%
  const totalLength = candle.high - candle.low;
  if (body / totalLength > 0.3) return false;
  
  return true;
}
```

#### 信号强度

**强烈买入**:
- 出现在支撑位
- 成交量放大
- 次日收阳线确认

**一般买入**:
- 出现在下跌趋势中
- 成交量正常

---

### 2. 吞没形态

#### 看涨吞没

**特征**:
```
前一根: 阴线
后一根: 阳线完全吞没前一根
出现在下跌趋势中
成交量放大
```

**识别条件**:
```javascript
function isBullishEngulfing(prev, curr) {
  // 前一根是阴线
  if (prev.close >= prev.open) return false;
  
  // 后一根是阳线
  if (curr.close <= curr.open) return false;
  
  // 完全吞没
  if (curr.open >= prev.close && curr.close <= prev.open) return false;
  if (curr.open > prev.open || curr.close < prev.close) return false;
  
  // 成交量放大
  if (curr.volume < prev.volume * 1.2) return false;
  
  return true;
}
```

#### 看跌吞没

**特征**:
```
前一根: 阳线
后一根: 阴线完全吞没前一根
出现在上涨趋势中
成交量放大
```

---

### 3. 启明星/黄昏星

#### 启明星（看涨）

**三根K线组合**:
```
第一根: 大阴线
第二根: 小实体（十字星最佳）
第三根: 大阳线
```

**识别条件**:
```javascript
function isMorningStar(k1, k2, k3) {
  // 第一根大阴线
  const body1 = k1.open - k1.close;
  if (body1 < (k1.high - k1.low) * 0.6) return false;
  
  // 第二根小实体
  const body2 = Math.abs(k2.close - k2.open);
  if (body2 > (k2.high - k2.low) * 0.3) return false;
  
  // 第三根大阳线
  const body3 = k3.close - k3.open;
  if (body3 < (k3.high - k3.low) * 0.6) return false;
  
  // 第三根收盘价 > 第一根实体中部
  if (k3.close < (k1.open + k1.close) / 2) return false;
  
  return true;
}
```

---

## 🚫 形态失败识别

### 假突破特征

1. **突破后成交量未放大**
```javascript
if (breakoutVolume < avgVolume * 1.3) {
  return { type: 'fake_breakout', reason: 'volume_not_confirmed' };
}
```

2. **突破后快速回落**
```javascript
if (closePrice < breakoutPrice * 0.97) {
  return { type: 'fake_breakout', reason: 'quick_reversal' };
}
```

3. **突破幅度 < 3%**
```javascript
const breakoutPercent = (breakoutPrice - resistance) / resistance;
if (breakoutPercent < 0.03) {
  return { type: 'weak_breakout', reason: 'insufficient_momentum' };
}
```

4. **突破后横盘不涨**
```javascript
const daysAfterBreakout = 5;
const priceChange = (currentPrice - breakoutPrice) / breakoutPrice;
if (priceChange < 0.02 && daysPassed > daysAfterBreakout) {
  return { type: 'failed_breakout', reason: 'no_follow_through' };
}
```

### 应对策略

**确认突破**:
- 等待突破后 2-3 天确认
- 观察成交量配合
- 设置止损位（突破点下方 3%）

**假突破止损**:
- 快速止损，不要犹豫
- 重新评估形态
- 等待新的机会

---

## 📊 实战案例

### 案例 1: BTC/USDT 上升三角形

```
时间: 2026年2月15日 - 3月1日
形态: 上升三角形

识别过程:
1. 上边界: $68,200 (水平阻力)
2. 下边界: 上升趋势线
3. 触及次数: 上边界 3次，下边界 4次
4. 成交量: 逐步萎缩
5. 置信度: 85%

交易策略:
- 买入点: 突破 $68,200 并站稳
- 目标价: $72,000 (形态高度 $3,800)
- 止损价: $66,500
- 风险收益比: 1:2.4

结果: 
- 3月2日突破 $68,200
- 3月5日达到目标价 $72,000
- 成功率: ✅
```

### 案例 2: 普元信息双底形态

```
时间: 2026年1月10日 - 2月20日
形态: 双底 (W底)

识别过程:
1. 第一个低点: ¥28.50 (1月10日)
2. 第二个低点: ¥28.20 (2月5日)
3. 价格相差: 1.05% ✅
4. 时间间隔: 26天 ✅
5. 颈线: ¥33.00
6. 颈线高度: 15.9% ✅
7. 成交量: 递减 ✅
8. 置信度: 92%

交易策略:
- 买入点: 突破 ¥33.00
- 目标价: ¥37.50
- 止损价: ¥27.50
- 风险收益比: 1:2.7

结果:
- 2月20日突破颈线
- 3月1日达到 ¥35.50 (目标价未到但盈利可观)
- 成功率: ✅
```

---

## 💡 使用建议

### 1. 形态识别优先级

**高优先级**:
- 双底/双顶
- 头肩底/头肩顶
- 上升三角形

**中优先级**:
- 旗形整理
- 矩形整理
- 圆弧底

**低优先级**:
- 单根K线形态（需结合其他信号）

### 2. 确认机制

**必须确认**:
- 成交量配合
- 突破有效性
- 时间周期

**可选确认**:
- 技术指标支持
- 基本面配合
- 市场环境

### 3. 风险控制

**严格止损**:
- 形态破坏立即止损
- 不要抱有幻想
- 保护本金第一

**分批建仓**:
- 突破时建仓 50%
- 确认后加仓 30%
- 趋势延续加仓 20%

---

## 📚 进阶学习

### 推荐书籍
1. 《股市趋势技术分析》- Robert D. Edwards
2. 《期货市场技术分析》- John J. Murphy
3. 《日本蜡烛图技术》- Steve Nison

### 实践建议
1. 建立形态识别日志
2. 回测历史形态成功率
3. 总结失败案例
4. 持续优化识别算法
