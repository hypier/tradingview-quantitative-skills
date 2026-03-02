---
name: tradingview-quantitative
description: 专业的量化投资分析系统，基于 TradingView 数据提供智能选股、技术形态识别、市场复盘、风险管理和事件驱动分析。使用场景：(1) 股票筛选 - 当用户说"帮我选股"、"筛选股票"、"找符合条件的股票"时；(2) 形态分析 - 当用户说"分析形态"、"技术形态"、"有什么形态"时；(3) 市场复盘 - 当用户说"市场复盘"、"今天市场怎么样"、"有什么投资机会"时；(4) 风险管理 - 当用户说"仓位建议"、"风险评估"、"应该买多少"、"止损位"时；(5) 事件分析 - 当用户说"事件分析"、"影响分析"、"政策影响"、"财报影响"时；(6) 个股分析 - 当用户说"分析某只股票"、"看看XX怎么样"、"XX能买吗"时；(7) 基本面筛选 - 当用户说"高股息股票"、"低估值"、"财务分析"时；(8) 板块轮动 - 当用户说"板块轮动"、"哪个板块强"、"行业对比"时；(9) 多时间框架分析 - 当用户说"多周期分析"、"长短期趋势"时。提供多因子综合评分、形态置信度评估、交易策略建议和风险控制方案。
---

# 量化投资分析专家

专业的量化投资分析系统，基于 TradingView MCP 工具提供洞察和决策建议。

## 核心规则

### 元数据优先原则

**调用 `tradingview_get_leaderboard` 之前，必须先调用 `tradingview_get_metadata` 获取参数值：**

1. `type='markets'` → 获取 `market_code`（股票排行榜必需）
2. `type='tabs'` + `asset_type` → 获取可用的 `tab` 值
3. `type='columnsets'` → 获取可用的 `columnset` 值

完整的元数据字典（market codes、tabs、columnsets、exchanges）参见 `references/api-documentation.md`。

### 工具选择速查

| 需求 | 工具 | 关键参数 |
|------|------|---------|
| 搜索标的 | `search_market` | query, filter(stock/crypto/forex...) |
| 实时报价 | `get_quote` / `get_quote_batch` | symbol, session |
| K线数据 | `get_price` / `get_price_batch` | symbol, timeframe(1/5/15/30/60/240/D/W/M), range(max 500) |
| 技术分析 | `get_ta` | symbol, **include_indicators=true 获取详细指标** |
| 排行榜 | `get_leaderboard` | asset_type, tab, market_code, **columnset**(overview/performance/valuation/dividends/profitability/income_statement/balance_sheet/cash_flow/technical) |
| 新闻 | `get_news` / `get_news_detail` | market_country, lang(zh-Hans/en/ja), symbol |
| 财经日历 | `get_calendar` | type(economic/earnings/revenue/ipo), from/to(Unix秒), market |
| 元数据 | `get_metadata` | type(markets/tabs/columnsets/languages/exchanges) |

## 工作流

详细步骤请查看 `workflows/` 目录：

### 核心分析
- **`deep-stock-analysis.md`** - 个股深度分析（组合 quote + price多周期 + ta详细指标 + news + calendar）
- **`smart-screening.md`** - 智能选股（leaderboard多columnset + ta + price）
- **`fundamental-screening.md`** - 基本面筛选（leaderboard的valuation/profitability/dividends等columnset）
- **`pattern-recognition.md`** - 技术形态识别（price + ta + pattern-library参考）
- **`multi-timeframe-analysis.md`** - 多时间框架趋势确认（price D/W/M + ta多周期）

### 市场与板块
- **`market-review.md`** - 市场复盘（leaderboard gainers/losers + news）
- **`sector-rotation.md`** - 板块轮动分析（leaderboard performance columnset + 多板块对比）
- **`news-briefing.md`** - 财经新闻简报（news + news_detail，支持多国家多语言）

### 风险与事件
- **`risk-assessment.md`** - 风险评估（price历史数据 + quote + 波动率计算）
- **`event-analysis.md`** - 事件驱动分析（calendar + news + search）
- **`calendar-tracking.md`** - 日历事件追踪（calendar 4种类型）

### 行情与搜索
- **`symbol-search.md`** - 标的搜索（search_market）
- **`realtime-monitor.md`** - 实时行情监控（quote / quote_batch）
- **`multi-symbol-analysis.md`** - 多标的批量分析（quote_batch + price_batch + ta）
- **`exchange-overview.md`** - 交易所概览（metadata exchanges/markets/tabs）

## 参考知识库

专业方法论和数据字典请查看 `references/` 目录：

- **`api-documentation.md`** - TradingView API 完整文档（endpoints、参数、元数据字典：market codes/tabs/columnsets/exchanges，搜索关键词：`Market Codes`、`Asset Types and Tabs`、`Column Sets`、`Supported Languages`）
- **`mcp-tools-guide.md`** - MCP 工具使用指南（工具组合模式、元数据优先规则、各场景最佳实践）
- **`technical-analysis.md`** - 技术分析方法论（综合评分模型、趋势/动量/形态/支撑阻力评分，搜索关键词：`综合评分模型`、`RSI`、`MACD`、`支撑阻力`）
- **`pattern-library.md`** - 形态识别库（经典形态、识别算法、成功率统计，搜索关键词：`双底`、`头肩底`、`三角形`、`旗形`、`K线形态`）
- **`risk-management.md`** - 风险管理体系（仓位管理、止损策略、组合管理，搜索关键词：`凯利公式`、`波动率`、`止损止盈`、`分批建仓`）
- **`china-a-stock-examples.md`** - A股实战案例（选股、形态分析、市场复盘的输出示例）

## 免责声明

本 Skill 提供的分析和建议**仅供参考**，不构成投资建议。投资有风险，决策需谨慎。
