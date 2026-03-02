# 风险管理体系

> 专业的仓位管理、止损止盈和组合风险控制方法

---

## 📊 风险管理三大支柱

1. **仓位管理** - 控制单笔交易风险
2. **止损止盈** - 保护利润，限制损失
3. **组合管理** - 分散风险，优化收益

---

## 1️⃣ 仓位管理

### 1.1 凯利公式

#### 公式
```
最优仓位 = (胜率 × 盈亏比 - 败率) / 盈亏比

其中:
- 胜率 = 盈利次数 / 总交易次数
- 败率 = 1 - 胜率
- 盈亏比 = 平均盈利 / 平均亏损
```

#### 示例计算
```javascript
function calculateKellyPosition(winRate, profitLossRatio) {
  const loseRate = 1 - winRate;
  const kellyPercent = (winRate * profitLossRatio - loseRate) / profitLossRatio;
  
  // 保守起见，使用半凯利
  const conservativeKelly = kellyPercent * 0.5;
  
  return Math.max(0, Math.min(conservativeKelly, 0.25)); // 最大25%
}

// 示例
const winRate = 0.6;        // 60% 胜率
const plRatio = 2;          // 2:1 盈亏比
const position = calculateKellyPosition(winRate, plRatio);
// 结果: (0.6 × 2 - 0.4) / 2 = 0.4 → 半凯利 = 0.2 (20%)
```

#### 实际应用建议
```
理论凯利仓位 × 0.5 = 实际仓位 (保守)
单只股票最大仓位: 20%
单个板块最大仓位: 40%
总仓位控制: 见市场环境调整
```

---

### 1.2 固定比例法

#### 方法
```
仓位 = 总资金 × 固定比例
```

#### 风险等级划分

| 风险等级 | 单笔仓位 | 适用人群 |
|---------|---------|---------|
| 保守型 | 5-10% | 风险厌恶者 |
| 平衡型 | 10-15% | 一般投资者 |
| 激进型 | 15-20% | 风险偏好者 |
| 极端型 | >20% | 专业交易者 |

---

### 1.3 波动率调整法

#### 公式
```
调整后仓位 = 基础仓位 × (标准波动率 / 实际波动率)

标准波动率 = 30%
```

#### 实现
```javascript
function adjustPositionByVolatility(basePosition, volatility) {
  const standardVol = 0.30;  // 30% 标准波动率
  const adjustment = standardVol / volatility;
  
  return basePosition * Math.min(adjustment, 1.5); // 最多放大1.5倍
}

// 示例
const basePosition = 0.15;  // 15% 基础仓位
const volatility = 0.45;    // 45% 波动率（高波动）
const adjusted = adjustPositionByVolatility(basePosition, volatility);
// 结果: 0.15 × (0.30 / 0.45) = 0.10 (10%)
```

---

### 1.4 金字塔加仓

#### 正金字塔（推荐）
```
第一笔: 50% 仓位 - 在支撑位买入
第二笔: 30% 仓位 - 盈利 5% 后加仓
第三笔: 20% 仓位 - 盈利 10% 后加仓
```

**优点**:
- 风险可控
- 成本较低
- 适合趋势交易

**示例**:
```javascript
function pyramidBuying(capital, currentPrice, entryPrice, position) {
  const profit = (currentPrice - entryPrice) / entryPrice;
  
  if (position === 0) {
    // 第一笔：50%
    return { amount: capital * 0.5, reason: '初始建仓' };
  } else if (position === 1 && profit >= 0.05) {
    // 第二笔：30%
    return { amount: capital * 0.3, reason: '盈利5%加仓' };
  } else if (position === 2 && profit >= 0.10) {
    // 第三笔：20%
    return { amount: capital * 0.2, reason: '盈利10%加仓' };
  }
  
  return { amount: 0, reason: '不加仓' };
}
```

#### 倒金字塔（不推荐）
```
第一笔: 20% 仓位
第二笔: 30% 仓位
第三笔: 50% 仓位
```

**缺点**:
- 风险过大
- 成本较高
- 容易被套

---

### 1.5 分批建仓

#### 三分法
```
第一批: 33% - 在支撑位买入
第二批: 33% - 突破阻力位买入
第三批: 34% - 确认趋势后买入
```

#### 五分法（更保守）
```
每批: 20%
分5次买入
间隔: 根据市场情况调整
```

---

## 2️⃣ 止损策略

### 2.1 固定百分比止损

#### 标准
```
保守型: -5%
平衡型: -7%
激进型: -10%
```

#### 实现
```javascript
function calculateFixedStopLoss(entryPrice, riskLevel = 'balanced') {
  const stopLossPercent = {
    conservative: 0.05,
    balanced: 0.07,
    aggressive: 0.10
  };
  
  const percent = stopLossPercent[riskLevel] || 0.07;
  return entryPrice * (1 - percent);
}
```

---

### 2.2 技术止损

#### 方法

**1. 支撑位止损**
```
止损价 = 支撑位 × 0.98 (支撑位下方2%)
```

**2. 均线止损**
```
短线: 跌破 5日均线
中线: 跌破 20日均线
长线: 跌破 60日均线
```

**3. 形态止损**
```
双底: 第二个低点下方
三角形: 下边界下方
头肩底: 右肩下方
```

#### 实现
```javascript
function calculateTechnicalStopLoss(currentPrice, support, ma20, pattern) {
  const stops = [];
  
  // 支撑位止损
  if (support) {
    stops.push({
      price: support * 0.98,
      type: 'support',
      distance: (currentPrice - support * 0.98) / currentPrice
    });
  }
  
  // 均线止损
  if (ma20) {
    stops.push({
      price: ma20,
      type: 'ma20',
      distance: (currentPrice - ma20) / currentPrice
    });
  }
  
  // 形态止损
  if (pattern && pattern.stopLoss) {
    stops.push({
      price: pattern.stopLoss,
      type: 'pattern',
      distance: (currentPrice - pattern.stopLoss) / currentPrice
    });
  }
  
  // 选择最近的止损位（风险最小）
  return stops.sort((a, b) => b.price - a.price)[0];
}
```

---

### 2.3 时间止损

#### 规则
```
持有 30 天未盈利 → 考虑离场
持有 60 天未达预期 → 强制离场
```

#### 原因
- 资金效率低
- 机会成本高
- 可能判断错误

---

### 2.4 移动止损

#### 策略
```javascript
function calculateTrailingStop(entryPrice, currentPrice, highestPrice) {
  const profit = (currentPrice - entryPrice) / entryPrice;
  
  if (profit < 0.05) {
    // 盈利 < 5%: 止损在成本价
    return entryPrice;
  } else if (profit < 0.10) {
    // 盈利 5-10%: 止损在成本价 + 3%
    return entryPrice * 1.03;
  } else if (profit < 0.20) {
    // 盈利 10-20%: 止损在成本价 + 5%
    return entryPrice * 1.05;
  } else {
    // 盈利 > 20%: 止损在最高价 - 10%
    return highestPrice * 0.90;
  }
}
```

---

## 3️⃣ 止盈策略

### 3.1 分批止盈

#### 策略
```
盈利 10%: 卖出 30%
盈利 20%: 卖出 30%
盈利 30%: 卖出 40%
```

#### 优点
- 锁定部分利润
- 保留上涨空间
- 降低心理压力

#### 实现
```javascript
function calculatePartialTakeProfit(entryPrice, currentPrice, remainingPosition) {
  const profit = (currentPrice - entryPrice) / entryPrice;
  
  if (profit >= 0.30 && remainingPosition > 0.4) {
    return { sell: 0.4, reason: '盈利30%，卖出40%' };
  } else if (profit >= 0.20 && remainingPosition > 0.7) {
    return { sell: 0.3, reason: '盈利20%，卖出30%' };
  } else if (profit >= 0.10 && remainingPosition === 1.0) {
    return { sell: 0.3, reason: '盈利10%，卖出30%' };
  }
  
  return { sell: 0, reason: '持有' };
}
```

---

### 3.2 追踪止盈

#### 策略
```
盈利 > 15% 后
回撤 5% 离场
```

#### 实现
```javascript
function calculateTrailingTakeProfit(entryPrice, currentPrice, highestPrice) {
  const profit = (currentPrice - entryPrice) / entryPrice;
  
  if (profit < 0.15) {
    return { action: 'hold', reason: '盈利未达15%' };
  }
  
  const drawdown = (highestPrice - currentPrice) / highestPrice;
  
  if (drawdown >= 0.05) {
    return { action: 'sell', reason: '回撤5%，追踪止盈' };
  }
  
  return { action: 'hold', reason: '继续持有' };
}
```

---

### 3.3 目标价止盈

#### 策略
```
第一目标: 卖出 50%
第二目标: 卖出 30%
第三目标: 卖出 20%
```

#### 目标价计算
```javascript
function calculateTargetPrices(entryPrice, pattern, resistance) {
  const targets = [];
  
  // 基于形态的目标价
  if (pattern && pattern.target) {
    targets.push({
      price: pattern.target,
      type: 'pattern',
      sellPercent: 0.5
    });
  }
  
  // 基于阻力位的目标价
  if (resistance) {
    targets.push({
      price: resistance,
      type: 'resistance',
      sellPercent: 0.3
    });
  }
  
  // 基于风险收益比的目标价
  const riskRewardTarget = entryPrice * 1.20; // 20% 目标
  targets.push({
    price: riskRewardTarget,
    type: 'risk_reward',
    sellPercent: 0.2
  });
  
  return targets.sort((a, b) => a.price - b.price);
}
```

---

## 4️⃣ 风险评估

### 4.1 个股风险评估

#### 波动率风险
```javascript
function calculateVolatility(priceHistory) {
  const returns = [];
  for (let i = 1; i < priceHistory.length; i++) {
    const ret = (priceHistory[i].close - priceHistory[i-1].close) / priceHistory[i-1].close;
    returns.push(ret);
  }
  
  const mean = returns.reduce((a, b) => a + b) / returns.length;
  const variance = returns.reduce((sum, ret) => 
    sum + Math.pow(ret - mean, 2), 0) / returns.length;
  
  const dailyVol = Math.sqrt(variance);
  const annualVol = dailyVol * Math.sqrt(252);
  
  return {
    daily: dailyVol,
    annual: annualVol,
    level: annualVol < 0.2 ? 'low' : annualVol < 0.4 ? 'medium' : 'high'
  };
}
```

#### 风险等级
```
低风险: 年化波动率 < 20%
中风险: 年化波动率 20-40%
高风险: 年化波动率 > 40%
```

---

#### 流动性风险
```
良好: 日均成交额 > 1亿
一般: 日均成交额 5000万-1亿
较差: 日均成交额 < 5000万
```

---

#### Beta 系数
```
低风险: Beta < 0.8
中风险: Beta 0.8-1.2
高风险: Beta > 1.2
```

---

### 4.2 组合风险评估

#### 分散度检查
```javascript
function checkDiversification(portfolio) {
  const checks = {
    stockCount: portfolio.length >= 5 && portfolio.length <= 10,
    sectorCount: new Set(portfolio.map(p => p.sector)).size >= 3,
    singleStockMax: Math.max(...portfolio.map(p => p.weight)) <= 0.20,
    sectorMax: checkSectorConcentration(portfolio) <= 0.40
  };
  
  return {
    score: Object.values(checks).filter(Boolean).length / 4,
    checks
  };
}
```

#### 相关性检查
```javascript
function calculateCorrelation(returns1, returns2) {
  const n = returns1.length;
  const mean1 = returns1.reduce((a, b) => a + b) / n;
  const mean2 = returns2.reduce((a, b) => a + b) / n;
  
  let numerator = 0;
  let denom1 = 0;
  let denom2 = 0;
  
  for (let i = 0; i < n; i++) {
    const diff1 = returns1[i] - mean1;
    const diff2 = returns2[i] - mean2;
    numerator += diff1 * diff2;
    denom1 += diff1 * diff1;
    denom2 += diff2 * diff2;
  }
  
  return numerator / Math.sqrt(denom1 * denom2);
}

// 相关性标准
// < 0.3: 低相关（好）
// 0.3-0.7: 中等相关（可接受）
// > 0.7: 高相关（需要调整）
```

---

## 5️⃣ 风险收益比

### 5.1 计算方法

```javascript
function calculateRiskRewardRatio(entryPrice, targetPrice, stopLoss) {
  const potentialProfit = targetPrice - entryPrice;
  const potentialLoss = entryPrice - stopLoss;
  
  return {
    ratio: potentialProfit / potentialLoss,
    profit: (potentialProfit / entryPrice * 100).toFixed(2) + '%',
    loss: (potentialLoss / entryPrice * 100).toFixed(2) + '%'
  };
}
```

### 5.2 评级标准

| 风险收益比 | 评级 | 建议 |
|-----------|------|------|
| > 1:3 | 优秀 | 强烈推荐 |
| 1:2 - 1:3 | 良好 | 推荐 |
| 1:1.5 - 1:2 | 一般 | 可接受 |
| < 1:1.5 | 较差 | 不推荐 |

### 5.3 最低要求

```
风险收益比 ≥ 1:2
胜率 ≥ 50%
```

---

## 6️⃣ 资金管理

### 6.1 总仓位控制

#### 市场环境调整
```
牛市: 70-90%
震荡市: 50-70%
熊市: 20-40%
```

#### 实现
```javascript
function adjustTotalPosition(marketTrend, basePosition) {
  const adjustments = {
    bull: 1.2,      // 牛市放大20%
    neutral: 1.0,   // 震荡市不变
    bear: 0.6       // 熊市缩小40%
  };
  
  const adjusted = basePosition * (adjustments[marketTrend] || 1.0);
  return Math.min(adjusted, 0.90); // 最大90%
}
```

---

### 6.2 现金储备

#### 最低现金
```
最低现金储备: 10%
```

#### 用途
- 加仓机会
- 应急资金
- 心理安全垫

---

### 6.3 杠杆使用

#### 建议
```
不建议使用杠杆
如必须使用: 杠杆倍数 ≤ 1.5倍
```

#### 风险
- 放大损失
- 强制平仓
- 心理压力

---

## 📊 实战案例

### 案例 1: 完整风险管理流程

```
股票: 普元信息 (688118)
资金: ¥100,000
当前价格: ¥35.50

【第一步：风险评估】
- 波动率: 45.2% (高风险)
- Beta: 2.19 (高风险)
- 流动性: 良好

【第二步：仓位计算】
- 基础仓位: 15% (平衡型)
- 波动率调整: 15% × (30% / 45%) = 10%
- 建议仓位: 8-10%

【第三步：分批建仓】
第一批: 5% (¥5,000) - 价格 ¥35.00-35.50
第二批: 5% (¥5,000) - 突破 ¥37.00 或回调至 ¥33.50

【第四步：止损设置】
- 技术止损: ¥33.00 (支撑位)
- 百分比止损: ¥33.00 (-7%)
- 最终止损: ¥33.00

【第五步：止盈设置】
- 第一目标: ¥40.00 (+12.7%) - 卖出 50%
- 第二目标: ¥45.00 (+26.8%) - 卖出 30%
- 追踪止盈: 盈利 > 15% 后，回撤 5% 离场

【第六步：风险收益比】
- 潜在收益: +12.7% (第一目标)
- 潜在风险: -7%
- 风险收益比: 1:1.8 ✅

【执行结果】
- 买入价: ¥35.20
- 买入数量: 142股 (¥5,000)
- 止损价: ¥33.00
- 目标价: ¥40.00
```

---

## 💡 风险管理原则

### 1. 永远不要满仓

**原因**:
- 失去灵活性
- 无法应对机会
- 心理压力巨大

---

### 2. 严格执行止损

**原则**:
- 止损不是建议，是纪律
- 不要移动止损位（除非向上）
- 止损后不要马上买回

---

### 3. 保护利润

**方法**:
- 盈利后上移止损
- 分批止盈
- 不要让盈利变亏损

---

### 4. 控制回撤

**目标**:
- 单笔最大亏损: < 2%
- 月度最大回撤: < 10%
- 年度最大回撤: < 20%

---

### 5. 记录和复盘

**内容**:
- 每笔交易记录
- 止损原因分析
- 成功失败总结
- 持续优化策略

---

## 📚 推荐阅读

1. 《海龟交易法则》- Curtis Faith
2. 《通向财务自由之路》- Van K. Tharp
3. 《交易心理分析》- Mark Douglas
4. 《专业投机原理》- Victor Sperandeo
