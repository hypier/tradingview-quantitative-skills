---
description: 市场复盘工作流 - 每日市场分析和投资机会发现
---

# 市场复盘工作流

每日市场复盘，追踪热点板块和资金流向，发现投资机会。

## 执行步骤

### 步骤 1: 获取元数据

```
tradingview_get_metadata(type='markets')  # 确认 market_code
```

### 步骤 2: 获取涨跌榜数据

并行调用获取多维度数据：

```
# 涨幅榜
tradingview_get_leaderboard(
  asset_type='stocks', tab='gainers',
  market_code='china', columnset='overview', count=50
)

# 跌幅榜
tradingview_get_leaderboard(
  asset_type='stocks', tab='losers',
  market_code='china', columnset='overview', count=50
)

# 活跃股（成交量最大）
tradingview_get_leaderboard(
  asset_type='stocks', tab='active',
  market_code='china', columnset='overview', count=30
)

# 异常放量
tradingview_get_leaderboard(
  asset_type='stocks', tab='unusual-volume',
  market_code='china', columnset='overview', count=30
)
```

### 步骤 3: 获取市场新闻

```
tradingview_get_news(market='stock', market_country='CN', lang='zh-Hans', limit=10)
```

对重要新闻获取详情：
```
tradingview_get_news_detail(news_id, lang='zh-Hans')
```

### 步骤 4: 获取指数行情（可选）

```
tradingview_get_quote_batch(
  symbols=["SSE:000001", "SZSE:399001", "SZSE:399006"]  # 上证/深证/创业板
)
```

### 步骤 5: 识别热点板块

分析涨幅榜中的行业分布：
- 将涨幅前50只股票按行业归类
- 统计各行业在涨幅榜中的数量和平均涨幅
- 识别占比最高的 3-5 个板块

### 步骤 6: 分析资金流向

通过成交量数据分析：
- 活跃股和异常放量股的行业分布
- 涨幅榜 vs 跌幅榜的成交量对比
- 放量上涨的个股（资金流入信号）

### 步骤 7: 关联新闻与行情

将新闻中的公司/行业与涨跌榜关联：
- 涨停股的新闻催化剂
- 板块异动的政策/事件驱动
- 潜在持续性评估

### 步骤 8: 生成复盘报告

```markdown
# [市场] 市场复盘 - YYYY-MM-DD

## 市场概览
- 指数表现: [上证/深证/创业板涨跌幅]
- 涨跌家数: XX涨 / XX跌
- 成交额: XX亿（环比 +/-XX%）

## 热点板块（按强度排名）
| 排名 | 板块 | 涨幅 | 代表个股 | 催化剂 |
|------|------|------|---------|-------|

## 涨幅榜 Top 10
| 排名 | 股票 | 涨幅 | 成交量 | 异常信号 |
|------|------|------|--------|---------|

## 资金流向
- 主力流入板块: ...
- 主力流出板块: ...
- 异常放量个股: ...

## 新闻要点
1. [新闻标题] - [影响分析]
2. ...

## 投资机会
- [机会1]: 原因和建议标的
- [机会2]: ...

## 风险提示
- [风险1]
- [风险2]
```

## 示例

**用户**: "今天A股市场怎么样？"

**执行**:
1. `get_metadata(type='markets')` → china
2. `get_leaderboard` × 4（gainers/losers/active/unusual-volume）
3. `get_news(market_country='CN', lang='zh-Hans')` + 详情
4. `get_quote_batch` → 指数行情
5. 板块归类 → 热点识别 → 新闻关联
6. 生成复盘报告
