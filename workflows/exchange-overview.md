---
description: 交易所概览工作流 - 查看交易所信息和市场配置
---

# 交易所概览工作流

查看 TradingView 支持的交易所列表、市场配置和可用资产类型。

## 执行步骤

### 步骤 1: 确定查询类型

根据用户需求确定查询类型：
- **exchanges**: 查看交易所列表
- **markets**: 查看市场代码
- **tabs**: 查看排行榜分类标签
- **columnsets**: 查看数据列配置
- **languages**: 查看支持的语言

### 步骤 2: 调用元数据工具

调用 `tradingview_get_metadata` 获取配置信息：

```
参数说明：
- type: 元数据类型（必填）
  - markets: 市场代码列表（68+ 个市场）
  - tabs: 排行榜标签（按资产类型分类）
  - columnsets: 数据列配置（按资产类型分类）
  - languages: 支持的语言列表
  - exchanges: 交易所列表（353+ 个交易所）
- asset_type: 资产类型过滤（可选，仅用于 tabs）
  - stocks, indices, crypto, futures, forex, bonds, corporate_bonds, etfs
```

### 步骤 3: 格式化输出

根据查询类型格式化输出：

**交易所列表**：
- 证券交易所：NASDAQ、NYSE、HKEX、SSE、SZSE、TSE、LSE 等
- 加密货币交易所：Binance、Coinbase、Kraken、OKX、Bybit 等

**市场代码**：
- 北美：america、canada
- 欧洲：uk、germany、france
- 亚洲：china、japan、korea、india
- 其他：australia、brazil 等

**排行榜标签**：
- 股票：gainers、losers、large-cap、active 等
- 加密货币：all、gainers、losers、large-cap 等
- 其他资产类型的标签

### 步骤 4: 提供使用指引

指导用户如何使用获取的信息：
- 如何使用市场代码查询排行榜
- 如何使用交易所前缀搜索标的
- 如何选择合适的排行榜标签

## 示例对话

**用户**: "TradingView 支持哪些交易所？"

**执行**:
1. 调用 `tradingview_get_metadata`，type="exchanges"
2. 分类展示证券交易所和加密货币交易所
3. 说明交易所总数（353+）

---

**用户**: "查询美股市场有哪些排行榜？"

**执行**:
1. 调用 `tradingview_get_metadata`，type="tabs"，asset_type="stocks"
2. 展示股票排行榜标签：gainers、losers、large-cap 等
3. 说明如何使用这些标签查询排行榜

---

**用户**: "支持哪些国家的股票市场？"

**执行**:
1. 调用 `tradingview_get_metadata`，type="markets"
2. 展示市场代码列表：america、china、japan、uk 等
3. 说明市场代码用于排行榜查询

---

**用户**: "排行榜可以显示哪些数据列？"

**执行**:
1. 调用 `tradingview_get_metadata`，type="columnsets"
2. 按资产类型展示可用数据列：
   - 股票：overview、performance、valuation、dividends 等
   - 加密货币：overview、performance 等
3. 说明如何在排行榜查询中使用 columnset 参数
