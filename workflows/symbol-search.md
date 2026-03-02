---
description: 标的搜索工作流 - 快速搜索和发现交易标的
---

# 标的搜索工作流

快速搜索和发现股票、加密货币、外汇、期货等各类交易标的。

## 执行步骤

### 步骤 1: 解析搜索意图

从用户输入中提取搜索关键词和筛选条件：
- 关键词：股票名称、代码、拼音缩写等
- 资产类型：股票、加密货币、外汇、期货、指数、基金、债券、期权
- 语言偏好：中文或英文

### 步骤 2: 调用搜索工具

调用 `tradingview_search_market` 进行标的搜索：

```
参数说明：
- query: 搜索关键词（必填）
- filter: 资产类型过滤（可选）
  - stock: 股票
  - crypto: 加密货币
  - forex: 外汇
  - futures: 期货
  - index: 指数
  - funds: 基金
  - bond: 债券
  - options: 期权
- lang: 语言代码（默认 en）
- limit: 返回数量（默认 20，最大 100）
```

### 步骤 3: 格式化搜索结果

将搜索结果整理为易读格式，包含：
- 标的代码（如 NASDAQ:AAPL）
- 交易所名称
- 全称和描述
- 资产类型
- 货币代码
- 所属国家

### 步骤 4: 提供后续操作建议

根据搜索结果，建议用户可以进行的后续操作：
- 查看实时行情：`tradingview_get_quote`
- 查看技术分析：`tradingview_get_ta`
- 查看历史K线：`tradingview_get_price`
- 查看相关新闻：`tradingview_get_news`

## 示例对话

**用户**: "帮我搜索苹果公司的股票"

**执行**:
1. 调用 `tradingview_search_market`，query="AAPL"，filter="stock"
2. 返回结果包含 NASDAQ:AAPL 等匹配项
3. 建议用户可以查看实时行情或技术分析

---

**用户**: "搜索比特币相关的交易对"

**执行**:
1. 调用 `tradingview_search_market`，query="BTC"，filter="crypto"
2. 返回 BINANCE:BTCUSDT、COINBASE:BTCUSD 等交易对
3. 建议用户选择具体交易对进行深度分析
