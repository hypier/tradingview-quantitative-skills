# MCP 工具使用指南

> 元数据优先规则、工具组合模式、各场景最佳实践

---

## 元数据优先规则

调用需要参数值的工具前，先通过 `tradingview_get_metadata` 获取可用值。完整元数据字典参见 `api-documentation.md`（搜索 `Market Codes`、`Asset Types and Tabs`）。

### 何时必须调用 metadata

| 场景 | 调用方式 | 获取的参数 |
|------|---------|-----------|
| 查询股票排行榜 | `get_metadata(type='markets')` | market_code（如 america, china） |
| 查询任意排行榜 | `get_metadata(type='tabs', asset_type='stocks')` | tab（如 gainers, losers） |
| 需要非 overview 数据 | `get_metadata(type='columnsets')` | columnset（如 valuation, dividends） |
| 不确定有哪些交易所 | `get_metadata(type='exchanges')` | 353+ 交易所列表 |

### 常用 market_code 速查

无需每次调用 metadata，以下为常用值：

- **北美**: america, canada
- **欧洲**: uk, germany, france, switzerland, spain, italy
- **亚洲**: china, hong-kong, japan, korea, india, taiwan, singapore
- **其他**: australia, brazil

### 常用 columnset 速查

| columnset | 包含数据 | 适用场景 |
|-----------|---------|---------|
| overview | 价格、涨跌幅、市值、成交量 | 默认概览 |
| performance | 1周/1月/3月/6月/1年/YTD收益率 | 业绩对比、板块轮动 |
| valuation | PE、PB、PS、EV/EBITDA | 估值筛选 |
| dividends | 股息率、派息比率、除息日 | 高股息策略 |
| profitability | ROE、ROA、毛利率、净利率 | 盈利能力筛选 |
| income_statement | 营收、净利润、EPS | 财务分析 |
| balance_sheet | 总资产、负债率、流动比率 | 财务健康度 |
| cash_flow | 经营/投资/筹资现金流 | 现金流分析 |
| technical | RSI、Beta、SMA、ATR | 技术面概览 |

---

## 工具组合模式

### 模式 1: 个股深度分析

```
1. search_market(query="公司名") → 获取准确 symbol
2. get_quote(symbol) → 实时价格、涨跌、成交量
3. get_price(symbol, timeframe='D', range=120) → 日K线数据
4. get_ta(symbol, include_indicators=true) → 详细技术指标
5. get_news(symbol=symbol, lang='zh-Hans') → 相关新闻
6. get_calendar(type='earnings', from/to) → 近期财报日期
```

### 模式 2: 智能选股（技术面 + 基本面）

```
1. get_metadata(type='markets') → 确认 market_code
2. get_metadata(type='tabs', asset_type='stocks') → 确认 tab
3. get_leaderboard(asset_type='stocks', tab='gainers', market_code, columnset='overview') → 候选池
4. get_leaderboard(同上, columnset='valuation') → 估值数据
5. get_leaderboard(同上, columnset='profitability') → 盈利数据
6. 对 Top 候选: get_ta(symbol, include_indicators=true) → 技术面验证
7. 对 Top 候选: get_price(symbol, timeframe='D', range=60) → K线验证
```

### 模式 3: 多时间框架趋势确认

```
1. get_price(symbol, timeframe='M', range=24) → 月线趋势
2. get_price(symbol, timeframe='W', range=52) → 周线趋势
3. get_price(symbol, timeframe='D', range=120) → 日线趋势
4. get_price(symbol, timeframe='60', range=100) → 60分钟线细节
5. get_ta(symbol, include_indicators=true) → 多周期TA信号
```

信号一致性：月线/周线/日线趋势方向一致 → 高置信度

### 模式 4: 板块轮动分析

```
1. get_metadata(type='tabs', asset_type='stocks') → 获取所有tab
2. get_leaderboard(tab='best-performing', columnset='performance') → 各板块业绩
3. 对比不同 tab 的 performance columnset 数据
4. get_news(market='stock', market_country='CN') → 新闻确认热点
```

### 模式 5: 基本面筛选

```
1. get_leaderboard(tab='high-dividend', columnset='dividends') → 高股息
2. get_leaderboard(tab='all-stocks', columnset='valuation') → 低估值
3. get_leaderboard(tab='all-stocks', columnset='profitability') → 高ROE
4. 交叉筛选以上结果 → 价值股候选
```

### 模式 6: 市场复盘

```
1. get_leaderboard(tab='gainers', market_code, count=50) → 涨幅榜
2. get_leaderboard(tab='losers', market_code, count=50) → 跌幅榜
3. get_leaderboard(tab='active', market_code) → 活跃股
4. get_leaderboard(tab='unusual-volume', market_code) → 异常放量
5. get_news(market_country='CN', lang='zh-Hans', limit=10) → 新闻
6. 对每条新闻: get_news_detail(news_id) → 完整内容
```

---

## 关键参数说明

### get_price 时间框架选择

| timeframe | 含义 | 典型 range | 适用场景 |
|-----------|------|-----------|---------|
| 1 | 1分钟 | 60-240 | 日内交易 |
| 5 | 5分钟 | 48-120 | 短线分析 |
| 15 | 15分钟 | 48-96 | 短线分析 |
| 60 | 1小时 | 48-168 | 波段分析 |
| 240 | 4小时 | 30-90 | 波段分析 |
| D | 日线 | 60-250 | 中期分析 |
| W | 周线 | 52-104 | 中长期分析 |
| M | 月线 | 24-60 | 长期趋势 |

### get_price 图表类型

- 默认：标准K线
- `type='HeikinAshi'`：平均K线，过滤噪音，更清晰显示趋势方向

### get_ta include_indicators 返回字段

设置 `include_indicators=true` 返回的关键字段：

- **RSI(14)**: 相对强弱指标（>70超买, <30超卖）
- **MACD**: 趋势动量（DIF, DEA, 柱状图）
- **Stoch**: 随机指标（K, D值）
- **CCI(20)**: 商品通道指标
- **ADX(14)**: 趋势强度（>25有趋势, >50强趋势）
- **SMA/EMA**: 简单/指数移动平均线
- **Pivot Points**: 枢轴点（支撑/阻力位）

### get_news 语言代码

| 市场 | lang | market_country |
|------|------|---------------|
| 中国 | zh-Hans | CN |
| 美国 | en | US |
| 日本 | ja | JP |
| 香港 | zh-Hans 或 en | HK |
| 韩国 | ko | KR |

### get_calendar 时间戳

日历查询需要 Unix 时间戳（秒），时间跨度不超过 40 天：

```javascript
// 当前时间
const now = Math.floor(Date.now() / 1000);
// 未来7天
const weekLater = now + 7 * 24 * 60 * 60;
// 未来14天
const twoWeeksLater = now + 14 * 24 * 60 * 60;
```

---

## 多资产类型支持

MCP 支持 8 种资产类型，每种有不同的 tabs 和 columnsets：

| 资产类型 | asset_type | tabs数 | columnsets | 需要 market_code |
|---------|-----------|--------|-----------|-----------------|
| 股票 | stocks | 25 | 9种（含基本面） | 是 |
| 指数 | indices | 11 | 3种 | 否 |
| 加密货币 | crypto | 20 | 3种 | 否 |
| 期货 | futures | 7 | 2种 | 否 |
| 外汇 | forex | 10 | 3种 | 否 |
| 政府债券 | bonds | 17 | 2种 | 否 |
| 企业债券 | corporate_bonds | 6 | 1种 | 否 |
| ETF/基金 | etfs | 40 | 3种 | 否 |

### 加密货币特有 tabs

DeFi、TVL排名、地址数、交易量、供应量等 → 使用 `get_metadata(type='tabs', asset_type='crypto')` 查看完整列表。

### ETF 特有 tabs

按策略：bitcoin, gold, fixed-income, leveraged, inverse, sector 等 40 个分类。
