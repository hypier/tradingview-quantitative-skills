---
description: 板块轮动分析工作流 - 识别强势板块和轮动趋势
---

# 板块轮动分析工作流

通过 `tradingview_get_leaderboard` 的 performance columnset 和多种 tab，识别当前强势板块、分析轮动趋势、发现投资机会。

## 执行步骤

### 步骤 1: 获取元数据

```
tradingview_get_metadata(type='markets')   # 确认 market_code
tradingview_get_metadata(type='tabs', asset_type='stocks')  # 查看所有分类 tab
```

### 步骤 2: 获取板块涨跌榜（performance columnset）

```
# 涨幅榜 - performance 数据（1周/1月/3月/6月/1年收益率）
tradingview_get_leaderboard(
  asset_type='stocks', tab='best-performing',
  market_code='china', columnset='performance', count=50
)

# 跌幅榜
tradingview_get_leaderboard(
  asset_type='stocks', tab='losers',
  market_code='china', columnset='performance', count=50
)

# 活跃股
tradingview_get_leaderboard(
  asset_type='stocks', tab='active',
  market_code='china', columnset='overview', count=50
)

# 异常放量
tradingview_get_leaderboard(
  asset_type='stocks', tab='unusual-volume',
  market_code='china', columnset='overview', count=50
)
```

### 步骤 3: 板块归类分析

将涨幅榜股票按行业/板块归类，统计：
- 各板块在涨幅榜中的占比
- 各板块平均涨幅（1周/1月/3月维度）
- 板块内龙头股识别

### 步骤 4: 板块强度排名

通过 performance 数据对比各板块的多周期收益率：

| 板块 | 1周 | 1月 | 3月 | 6月 | 趋势判断 |
|------|-----|-----|-----|-----|---------|
| ... | ... | ... | ... | ... | 加速/减速/反转 |

判断逻辑：
- **加速上涨**: 短期 > 中期 > 长期收益率
- **减速上涨**: 长期 > 中期 > 短期收益率
- **反转向上**: 短期正、长期负
- **反转向下**: 短期负、长期正

### 步骤 5: 结合新闻确认

```
tradingview_get_news(market='stock', market_country='CN', lang='zh-Hans', limit=10)
```

对新闻进行板块关联分析，确认板块强势是否有基本面/政策面支撑。

### 步骤 6: 龙头股技术验证

对每个强势板块的龙头股（1-2只）：

```
tradingview_get_ta(symbol, include_indicators=true)
```

确认龙头股技术面是否支持板块趋势持续。

### 步骤 7: 生成板块轮动报告

```markdown
# 板块轮动分析报告

## 当前强势板块（按强度排名）
| 排名 | 板块 | 1周涨幅 | 1月涨幅 | 趋势阶段 | 龙头股 |
|------|------|---------|---------|---------|-------|

## 弱势板块
| 排名 | 板块 | 1周跌幅 | 趋势阶段 | 是否超跌 |
|------|------|---------|---------|---------|

## 轮动趋势判断
- 当前市场风格: [价值/成长/周期/防御]
- 资金流向: [从XX板块流向XX板块]
- 轮动阶段: [早期/中期/晚期]

## 投资建议
- 建议关注板块: ...
- 建议回避板块: ...
- 龙头股推荐: ...
```

## ETF/指数板块分析

也可使用指数和 ETF 数据做板块分析：

```
# 行业指数对比
tradingview_get_leaderboard(asset_type='indices', tab='all', columnset='performance')

# ETF 板块对比
tradingview_get_leaderboard(asset_type='etfs', tab='sector-etfs', columnset='performance')
tradingview_get_leaderboard(asset_type='etfs', tab='highest-returns', columnset='performance')
```
