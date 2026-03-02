---
description: 个股深度分析工作流 - 组合多工具对单个标的做全面分析
---

# 个股深度分析工作流

组合多个 MCP 工具，对单个标的做全面分析，输出综合报告和交易建议。

## 执行步骤

### 步骤 1: 确认标的

若用户输入的是中文名/简称，先搜索确认准确 symbol：

```
tradingview_search_market(query="用户输入", filter="stock", lang="zh")
```

确认 symbol 格式为 `EXCHANGE:SYMBOL`（如 `SSE:688118`、`NASDAQ:AAPL`）。

### 步骤 2: 获取实时行情

```
tradingview_get_quote(symbol, session="regular")
```

提取关键数据：
- 当前价、涨跌幅、成交量、成交额
- 买一/卖一价、买卖量
- 52周最高/最低、市值

### 步骤 3: 获取多时间框架K线

并行调用获取不同周期数据：

```
tradingview_get_price(symbol, timeframe='D', range=120)   # 日线 - 中期趋势
tradingview_get_price(symbol, timeframe='W', range=52)    # 周线 - 中长期
tradingview_get_price(symbol, timeframe='60', range=100)  # 60分钟 - 短期细节
```

可选：`type='HeikinAshi'` 获取平均K线，更清晰显示趋势。

### 步骤 4: 获取详细技术分析

```
tradingview_get_ta(symbol, include_indicators=true)
```

**关键指标解读**：
- 多周期信号汇总（1分钟/5分钟/15分钟/1小时/4小时/日/周/月）
- RSI(14)：>70 超买，<30 超卖
- MACD：金叉/死叉，DIF与0轴关系
- ADX(14)：>25 有趋势，>50 强趋势
- 均线排列：SMA5/10/20/60 相对关系

详细评分方法参见 `references/technical-analysis.md`。

### 步骤 5: 获取相关新闻

```
tradingview_get_news(symbol=symbol, lang="zh-Hans", limit=5)
```

对重要新闻获取详情：
```
tradingview_get_news_detail(news_id, lang="zh-Hans")
```

### 步骤 6: 查询近期事件

```
tradingview_get_calendar(type="earnings", from=now, to=now+30天, market="china")
```

检查是否有即将到来的财报、分红等事件。

### 步骤 7: 生成综合报告

输出结构：

```markdown
# [股票名称] (代码) 深度分析

## 基本信息
- 当前价 / 涨跌幅 / 成交量
- 市值 / 52周区间

## 技术面分析
- 多周期趋势判断（月/周/日/小时）
- 关键指标状态（RSI、MACD、ADX、均线）
- 支撑位 / 阻力位
- 综合技术评分（0-100）

## 形态识别
- 当前形态判断
- 置信度评估

## 新闻面
- 近期重要新闻摘要
- 影响评估

## 近期事件
- 财报日期 / 分红日期

## 交易建议
- 操作方向（买入/观望/卖出）
- 建议入场价
- 止损位 / 目标位
- 仓位建议
- 风险收益比

## 风险提示
```

## 示例

**用户**: "帮我分析一下普元信息"

**执行**:
1. `search_market(query="普元信息", filter="stock")` → SSE:688118
2. `get_quote(symbol="SSE:688118")` → 实时行情
3. `get_price` × 3个时间框架 → K线数据
4. `get_ta(include_indicators=true)` → 详细技术指标
5. `get_news(symbol="SSE:688118", lang="zh-Hans")` → 相关新闻
6. 综合评分，生成报告
