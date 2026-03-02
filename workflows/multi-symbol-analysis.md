---
description: 多标的批量分析工作流 - 批量分析多个标的的技术面和行情
---

# 多标的批量分析工作流

批量分析多个标的的技术面、行情数据，进行横向对比和综合评估。

## 执行步骤

### 步骤 1: 收集标的列表

从用户输入中提取需要分析的标的列表：
- 支持最多 10 个标的同时分析
- 标的格式：EXCHANGE:SYMBOL（如 NASDAQ:AAPL）

### 步骤 2: 批量获取实时行情

调用 `tradingview_get_quote_batch` 获取所有标的的实时行情：

```
参数说明：
- symbols: 标的数组（1-10个）
- session: 交易时段（默认 regular）
- fields: 返回字段（默认 all）
```

### 步骤 3: 批量获取历史K线

调用 `tradingview_get_price_batch` 获取所有标的的历史数据：

```
参数说明：
- requests: 请求数组（每个包含 symbol, timeframe, range）
- 最多 10 个请求
```

### 步骤 4: 逐个获取技术分析

对每个标的调用 `tradingview_get_ta` 获取技术分析信号：
- 汇总买入/卖出/中性信号
- 计算综合技术评分

### 步骤 5: 横向对比分析

对比各标的的关键指标：
- **涨跌幅对比**：谁涨得最多/跌得最多
- **成交活跃度**：谁的成交量最大
- **技术信号对比**：谁的技术面最强
- **波动率对比**：谁的波动最大

### 步骤 6: 生成对比报告

输出综合对比报告：
- 行情对比表格
- 技术面评分排名
- 投资价值排序
- 风险收益比对比
- 推荐优先关注的标的

## 示例对话

**用户**: "对比分析 AAPL、MSFT、GOOGL 三只股票"

**执行**:
1. 调用 `tradingview_get_quote_batch` 获取实时行情
2. 调用 `tradingview_get_price_batch` 获取日K数据
3. 分别调用 `tradingview_get_ta` 获取技术分析
4. 生成对比表格，标注各项指标最优的标的

---

**用户**: "批量分析科技龙头股的行情"

**执行**:
1. 确认科技龙头股列表（AAPL、MSFT、GOOGL、AMZN、META、NVDA、TSLA 等）
2. 批量获取行情和技术分析
3. 按涨跌幅、技术评分排序
4. 推荐技术面最强的标的

---

**用户**: "对比 BTC 和 ETH 的技术面"

**执行**:
1. 调用 `tradingview_get_quote_batch`，symbols=["BINANCE:BTCUSDT", "BINANCE:ETHUSDT"]
2. 调用 `tradingview_get_price_batch` 获取历史K线
3. 分别获取技术分析信号
4. 对比两者的趋势强度和超买超卖状态
