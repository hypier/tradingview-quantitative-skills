---
description: 基本面筛选工作流 - 利用排行榜多种 columnset 筛选价值股
---

# 基本面筛选工作流

利用 `tradingview_get_leaderboard` 的多种 columnset（valuation/profitability/dividends/balance_sheet/income_statement/cash_flow），从基本面维度筛选优质标的。

## 执行步骤

### 步骤 1: 确定筛选策略

根据用户需求确定筛选策略：

| 策略 | 核心 columnset | 关键指标 |
|------|---------------|---------|
| 价值投资 | valuation | 低 PE、低 PB、低 PS |
| 高股息 | dividends | 高股息率、稳定派息 |
| 成长股 | profitability + income_statement | 高 ROE、营收增长 |
| 财务健康 | balance_sheet + cash_flow | 低负债率、充裕现金流 |

### 步骤 2: 获取元数据

```
tradingview_get_metadata(type='markets')        # 获取 market_code
tradingview_get_metadata(type='columnsets')      # 确认可用 columnset
```

### 步骤 3: 获取排行榜数据（多 columnset）

根据策略组合调用，以价值投资为例：

```
# 获取估值数据
tradingview_get_leaderboard(
  asset_type='stocks', tab='all-stocks',
  market_code='china', columnset='valuation', count=100
)

# 获取盈利能力数据
tradingview_get_leaderboard(
  asset_type='stocks', tab='all-stocks',
  market_code='china', columnset='profitability', count=100
)

# 获取股息数据
tradingview_get_leaderboard(
  asset_type='stocks', tab='high-dividend',
  market_code='china', columnset='dividends', count=50
)
```

### 步骤 4: 交叉筛选

对多个 columnset 的结果进行交叉筛选：

**价值投资筛选条件示例**：
- PE < 20（合理估值）
- PB < 3（资产折价）
- ROE > 15%（盈利能力强）
- 股息率 > 2%（分红保障）

**成长股筛选条件示例**：
- 营收同比增长 > 20%
- 净利润增长 > 15%
- ROE > 20%
- 毛利率 > 40%

### 步骤 5: 技术面验证

对筛选出的 Top 候选（5-10只），逐个调用：

```
tradingview_get_ta(symbol, include_indicators=true)
```

过滤掉技术面明显走弱的标的（如 RSI > 80 超买、MACD 死叉）。

### 步骤 6: 生成筛选报告

```markdown
# 基本面筛选结果 - [策略名称]

## 筛选条件
- [列出所有条件]

## 符合条件的股票（共 N 只）

| 排名 | 股票 | PE | PB | ROE | 股息率 | 技术评分 |
|------|------|----|----|-----|--------|---------|
| 1 | ... | ... | ... | ... | ... | ... |

## 详细分析（Top 5）
### 1. [股票名称]
- 估值：...
- 盈利：...
- 技术面：...
- 买入建议：...
```

## 常用筛选组合

### 高股息策略
```
tab='high-dividend' + columnset='dividends' → 股息率排名
交叉 columnset='profitability' → 确认盈利可持续
交叉 columnset='balance_sheet' → 确认财务健康
```

### 低估值策略
```
tab='all-stocks' + columnset='valuation' → PE/PB 排名
交叉 columnset='income_statement' → 确认营收利润
交叉 columnset='cash_flow' → 确认现金流
```

### 白马股策略
```
tab='large-cap' + columnset='profitability' → 大盘高ROE
交叉 columnset='performance' → 确认业绩趋势
交叉 columnset='dividends' → 分红保障
```
