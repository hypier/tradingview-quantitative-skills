---
description: 实时行情监控工作流 - 获取和监控实时市场行情
---

# 实时行情监控工作流

获取和监控实时市场行情，包括价格、买卖盘、成交量等关键数据。

## 执行步骤

### 步骤 1: 确定监控标的

从用户输入中提取需要监控的标的列表：
- 单个标的：获取详细行情
- 多个标的：批量获取行情（最多10个）

### 步骤 2: 选择交易时段

根据用户需求选择交易时段：
- regular: 常规交易时段（默认）
- extended: 盘前盘后时段
- premarket: 仅盘前
- postmarket: 仅盘后

### 步骤 3: 获取实时行情

**单个标的**：调用 `tradingview_get_quote`

```
参数说明：
- symbol: 标的代码（必填，格式 EXCHANGE:SYMBOL）
- session: 交易时段（可选，默认 regular）
- fields: 返回字段（可选，默认 all）
```

**多个标的**：调用 `tradingview_get_quote_batch`

```
参数说明：
- symbols: 标的数组（必填，1-10个）
- session: 交易时段（可选，默认 regular）
- fields: 返回字段（可选，默认 all）
```

### 步骤 4: 解析行情数据

提取关键信息：
- **价格数据**：当前价、开盘价、最高价、最低价、昨收价
- **涨跌数据**：涨跌幅、涨跌额
- **成交数据**：成交量、成交额
- **买卖盘**：买一价、卖一价、买卖量
- **市场信息**：交易所、货币、交易状态

### 步骤 5: 生成行情报告

输出格式化的行情报告，包含：
- 标的基本信息
- 实时价格和涨跌情况
- 成交活跃度分析
- 买卖盘力量对比
- 异常波动提示

## 示例对话

**用户**: "查看苹果股票的实时行情"

**执行**:
1. 调用 `tradingview_get_quote`，symbol="NASDAQ:AAPL"
2. 返回当前价格、涨跌幅、成交量等数据
3. 分析买卖盘力量，给出简短评论

---

**用户**: "同时监控 AAPL、TSLA、NVDA 的行情"

**执行**:
1. 调用 `tradingview_get_quote_batch`，symbols=["NASDAQ:AAPL", "NASDAQ:TSLA", "NASDAQ:NVDA"]
2. 返回三只股票的实时行情对比
3. 标注涨跌幅最大和成交最活跃的标的

---

**用户**: "查看 BTCUSDT 的盘前行情"

**执行**:
1. 调用 `tradingview_get_quote`，symbol="BINANCE:BTCUSDT"，session="extended"
2. 返回盘前交易数据（如适用）
3. 对比常规时段价格变化
