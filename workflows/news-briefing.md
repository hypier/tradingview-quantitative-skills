---
description: 财经新闻简报工作流 - 获取并总结财经新闻
---

# 财经新闻简报工作流

获取指定国家/地区的财经新闻，分析影响并生成结构化简报。

## 执行步骤

### 步骤 1: 确定目标市场

常用 market_country 和 lang 组合：

| 市场 | market_country | lang |
|------|---------------|------|
| 中国 | CN | zh-Hans |
| 美国 | US | en |
| 日本 | JP | ja |
| 香港 | HK | zh-Hans 或 en |
| 韩国 | KR | ko |

### 步骤 2: 获取新闻列表

```
tradingview_get_news(
  market_country='CN', lang='zh-Hans', limit=10,
  market='stock'  # 可选过滤: stock/crypto/forex/futures/bond/etf/index/economic
)
```

也可按标的获取：
```
tradingview_get_news(symbol='NASDAQ:AAPL', lang='en', limit=10)
```

### 步骤 3: 获取新闻详情

对每条新闻获取完整内容：

```
tradingview_get_news_detail(news_id='tag:reuters.com,2026:newsml_xxx', lang='zh-Hans')
```

返回：标题、描述、完整内容、相关标的、标签、storyPath。

新闻链接格式：`https://www.tradingview.com{storyPath}`

### 步骤 4: 分析与总结

对每条新闻提取：
- 事件主体（公司/行业/政策）
- 影响范围和持续时间
- 受益/受损标的
- 投资建议

### 步骤 5: 生成简报

```markdown
# [国家/地区] 财经新闻简报 - YYYY-MM-DD

## 今日要闻

### 1. [新闻标题]
- 事件: [简要描述]
- 影响: [市场影响分析]
- 相关标的: [股票代码]
- 原文: https://www.tradingview.com{storyPath}

## 板块动态
| 板块 | 主要新闻 | 受影响标的 | 趋势 |
|------|---------|-----------|------|

## 投资机会与风险
- 机会: ...
- 风险: ...
```

## 示例

**用户**: "生成今日中国财经新闻简报"

**执行**:
1. `get_news(market_country='CN', lang='zh-Hans', limit=10)` → 新闻列表
2. 对每条 `get_news_detail(news_id, lang='zh-Hans')` → 完整内容
3. 分析影响、归类板块
4. 生成结构化简报

**用户**: "对比中美日三国财经新闻"

**执行**:
1. 分别 `get_news` 获取 CN/US/JP 新闻
2. 分别获取详情
3. 跨市场对比分析
4. 生成对比简报
