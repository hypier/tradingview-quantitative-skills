---
description: 智能选股工作流 - 基于多因子模型筛选优质标的
---

# 智能选股工作流

基于多因子模型，从市场中筛选出符合技术面和基本面条件的优质标的，提供综合评分和买入建议。

## 执行步骤

### 步骤 1: 理解筛选条件

从用户输入中提取：
- **市场范围**: 国家/地区（确定 market_code）、资产类型
- **技术面条件**: 趋势方向、RSI 区间、MACD 状态、成交量
- **基本面条件**: PE、PB、ROE、市值、股息率等
- **排序偏好**: 涨幅、市值、成交量等

### 步骤 2: 获取元数据（元数据优先）

```
tradingview_get_metadata(type='markets')  # 获取 market_code
tradingview_get_metadata(type='tabs', asset_type='stocks')  # 获取可用 tab
```

### 步骤 3: 获取候选池

根据筛选方向选择合适的 tab 和 columnset：

```
# 技术面选股 - 使用技术面相关 tab
tradingview_get_leaderboard(
  asset_type='stocks', tab='gainers',  # 或 active/unusual-volume/best-performing
  market_code='china', columnset='overview', count=100
)

# 基本面数据 - 切换 columnset
tradingview_get_leaderboard(
  asset_type='stocks', tab='all-stocks',
  market_code='china', columnset='valuation', count=100
)

# 盈利能力
tradingview_get_leaderboard(
  asset_type='stocks', tab='all-stocks',
  market_code='china', columnset='profitability', count=100
)
```

### 步骤 4: 技术面筛选

对候选池中的 Top 20-30 只，逐个调用：

```
tradingview_get_ta(symbol, include_indicators=true)
```

筛选条件（参见 `references/technical-analysis.md` 评分模型）：
- TA 多周期信号为 Buy
- RSI 在 30-70 健康区间
- MACD 金叉或 DIF > 0
- ADX > 25（存在趋势）

### 步骤 5: K线数据验证

对通过技术面筛选的 Top 10，获取K线确认：

```
tradingview_get_price(symbol, timeframe='D', range=60)
```

验证：
- 均线排列（多头/空头）
- 成交量配合（放量上涨）
- 距支撑位距离

### 步骤 6: 综合评分排序

按 `references/technical-analysis.md` 的评分模型计算总分（100分制）：
- 趋势强度 30分
- 动量指标 25分
- 形态识别 20分
- 支撑阻力 15分
- 市场情绪 10分

### 步骤 7: 生成详细报告

```markdown
# 智能选股结果 - [市场/板块]

## 筛选条件
- [列出技术面和基本面条件]

## 符合条件的股票（共 N 只）

### 1. [股票名称] (代码) ⭐⭐⭐⭐⭐
**综合评分**: XX/100
- 技术面: RSI=XX, MACD=XX, 趋势=XX
- 基本面: PE=XX, ROE=XX%
- 买入建议: ¥XX-XX
- 止损位: ¥XX
- 目标价: ¥XX
```

## 示例

**用户**: "帮我从A股中筛选强势股"

**执行**:
1. `get_metadata(type='markets')` → china
2. `get_leaderboard(tab='gainers', market_code='china', count=100)` → 涨幅榜
3. `get_leaderboard(tab='gainers', market_code='china', columnset='valuation')` → 估值
4. Top 20 逐个 `get_ta(include_indicators=true)` → 技术筛选
5. Top 10 `get_price(timeframe='D', range=60)` → K线验证
6. 综合评分 → 输出 Top 10 报告
