---
description: 事件驱动分析工作流 - 追踪重大事件并分析市场影响
---

# 事件驱动分析工作流

追踪重大事件，分析对市场的影响，识别受益标的，提供投资建议。

## 执行步骤

### 步骤 1: 获取财经日历

根据事件类型调用日历（时间跨度不超过40天）：

```
# 财报日历
tradingview_get_calendar(type='earnings', from=now, to=now+14天, market='china')

# 分红日历
tradingview_get_calendar(type='revenue', from=now, to=now+14天, market='china')

# 经济数据日历
tradingview_get_calendar(type='economic', from=now, to=now+7天, market='america,china')

# IPO 日历
tradingview_get_calendar(type='ipo', from=now, to=now+14天, market='china')
```

### 步骤 2: 获取相关新闻

```
# 获取特定市场新闻
tradingview_get_news(market='stock', market_country='CN', lang='zh-Hans', limit=20)

# 获取特定标的新闻
tradingview_get_news(symbol='SSE:600519', lang='zh-Hans', limit=10)

# 获取经济新闻
tradingview_get_news(market='economic', lang='zh-Hans', limit=10)
```

对重要新闻获取详情：
```
tradingview_get_news_detail(news_id, lang='zh-Hans')
```

### 步骤 3: 提取事件关键词

从日历事件和新闻中提取：
- **公司名/代码**: 直接关联的标的
- **行业关键词**: 用于搜索同行业受益股
- **政策关键词**: 用于扩展影响范围
- **事件类型**: 财报超预期/政策利好/行业事件/突发事件

### 步骤 4: 搜索受益标的

用提取的关键词搜索相关标的：

```
tradingview_search_market(query='关键词', filter='stock', limit=20)
```

对于行业事件，还可通过排行榜查找同板块股票：
```
tradingview_get_leaderboard(
  asset_type='stocks', tab='gainers',
  market_code='china', columnset='overview', count=50
)
```

### 步骤 5: 分析受益标的行情

对识别出的受益标的（5-10只），获取行情和技术面：

```
tradingview_get_quote_batch(symbols=[...])  # 实时行情
tradingview_get_ta(symbol, include_indicators=true)  # 技术面确认
```

### 步骤 6: 评估影响程度

对每个受益标的评估：

| 因素 | 权重 | 评估维度 |
|------|------|---------|
| 事件重要性 | 30% | 对行业/公司的根本性影响 |
| 相关度 | 25% | 标的与事件的直接关联程度 |
| 时效性 | 20% | 事件影响的持续时间 |
| 预期差 | 15% | 市场是否已充分定价 |
| 确定性 | 10% | 事件落地的确定性 |

影响等级：
- **强正面（+3）**: 直接受益，业绩有明确提升
- **正面（+2）**: 间接受益，行业景气度提升
- **轻微正面（+1）**: 情绪面利好
- **中性（0）**: 影响有限
- **负面（-1/-2/-3）**: 对称的负面影响

### 步骤 7: 生成分析报告

```markdown
# 事件驱动分析报告

## 事件概述
- 事件: [事件描述]
- 时间: [发生/预期时间]
- 类型: [财报/政策/行业/突发]
- 重要性: [高/中/低]

## 影响分析
- 直接影响: ...
- 间接影响: ...
- 影响持续时间: [短期/中期/长期]

## 受益标的分析

### 1. [标的名称] (代码) - 影响等级: +3
- 关联度: 直接受益，[原因]
- 当前价: ¥XX（涨跌幅 XX%）
- 技术面: RSI=XX, TA信号=[Buy/Sell]
- 操作建议: ...

### 2. [标的名称] ...

## 风险因素
- [风险1: 事件落地不及预期]
- [风险2: 市场已充分定价]

## 操作建议
- 短期策略: ...
- 中期策略: ...
```

## 示例

**用户**: "分析云计算涨价对相关公司的影响"

**执行**:
1. `get_news(market='stock', market_country='CN', lang='zh-Hans')` → 相关新闻
2. `get_news_detail(news_id)` → 新闻详情
3. `search_market(query='云计算', filter='stock')` → 相关标的
4. `get_leaderboard(tab='gainers', market_code='china')` → 涨幅榜验证
5. `get_quote_batch` + `get_ta` → 行情和技术面
6. 评估影响程度 → 生成分析报告
