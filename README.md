# A 股财经雷达（`@local/ashare-finance`）

面向 DeepSeek Harness 的 A 股研究插件：自动抓取关键财经消息，分析市场环境，收集并研读上市公司财报，并根据用户的风险偏好与期限生成研究型投资分析。

## 能力

| 工具 | 用途 |
| --- | --- |
| `ashare_market` | 指数、涨跌家数、行业领涨领跌、市场温度 |
| `ashare_news` | 政策/市场/公司/自选股消息，按重要度与情绪打分 |
| `ashare_quote` | 实时行情、估值、均线、波动率、回撤、RSI、支撑压力 |
| `ashare_financials` | 多期主要指标、三表摘要、财务研读结论、公告索引 |
| `ashare_report_read` | 定期报告正文摘录（管理层讨论、风险、关键数字）与 PDF |
| `ashare_research` | 近一年卖方研报、评级分布、EPS/PE 一致预期 |
| `ashare_advice` | 按风险偏好/期限评分，给出理由、风险、仓位与情景框架 |
| `ashare_briefing` | 一键生成盘前/盘后今日简报 |

Web 端在 Harness 页面右下角提供「A 股财经雷达」浮层，展示市场温度、关键消息和自选股；Host 端每 10 分钟自动预热新闻与行情缓存。

## 数据来源

- 东方财富：7×24 快讯、要闻、行情、F10 财务数据、公告正文、研报。
- 新浪财经：日线历史行情。
- 腾讯行情：行情与 K 线备用源。

均为公开、非官方承诺稳定性的接口；插件对单源失败做降级，并在结果中标注数据时间与告警。

## 安装

在 Harness 中通过插件管理器安装本目录：

```
plugin_manager install_bundle  E:\dsh_workspace\money\ashare-finance
```

或把目录路径作为 `install_bundle` 的 `target`。安装后新会话即可看到工具；Web 面板如未出现，刷新页面。

### 从 GitHub 安装

```bash
git clone https://github.com/<your-name>/ashare-finance.git
```

然后在 Harness 中执行：

```text
plugin_manager install_bundle /absolute/path/to/ashare-finance
```

安装后刷新一次 Web 页面，右下角会出现「A 股财经雷达」浮层。

### 兼容性

当前版本在 DeepSeek Harness `0.2.1-rc.2`（Windows / Node.js 20+）环境验证。插件不依赖第三方 npm 包，安装时不需要额外的构建步骤。

## 发布到 GitHub

本目录已经包含 `.gitignore`、`.gitattributes`、MIT LICENSE 和 GitHub Actions 语法检查。创建空仓库后执行：

```bash
cd ashare-finance
git init -b main
git add .
git commit -m "feat: initial A-share finance radar plugin"
git remote add origin https://github.com/<your-name>/ashare-finance.git
git push -u origin main
```

如果希望同时提供可下载的 Release 资产：

```bash
mkdir -p dist
pnpm pack --pack-destination dist
git archive --format=zip --output=dist/ashare-finance-source.zip HEAD
```

把 `dist/*.tgz` 和 `dist/*-source.zip` 上传到 GitHub Releases 即可。

## 目录结构

```text
ashare-finance/
├─ index.js                 # Host 入口、后台刷新、Web JSON 路由
├─ client.js                # Web 端浮层
├─ lib/
│  ├─ http.js               # 带超时/重试的公开接口请求
│  ├─ symbols.js            # 代码解析、行情、日线
│  ├─ news.js               # 新闻抓取、去重、重要度/情绪评分
│  ├─ market.js             # 指数、涨跌家数、行业、市场温度
│  ├─ finance.js            # 财务、公告正文、研报
│  ├─ analysis.js           # 技术指标、评分、研究结论
│  ├─ format.js             # Markdown 输出
│  ├─ briefing.js           # 今日简报聚合
│  └─ tools.js              # 8 个模型工具
├─ locale/{zh,en}.json      # 插件展示元数据
├─ icon.svg
├─ cordis.patch.yml
└─ package.json
```

## 配置

插件没有必填配置。默认自选为：

```
600519 贵州茅台, 300750 宁德时代, 000001 平安银行,
601318 中国平安, 000858 五粮液, 002594 比亚迪
```

工具参数可临时覆盖自选；`ashare_briefing` 支持 `watchlist` 数组。

## 风险说明

插件输出是公开数据 + 规则化评分的研究辅助，不构成证券买卖建议或收益承诺。新闻摘要与情绪标签可能误判，财务数据可能因追溯调整而不可直接比较；关键结论请回到交易所公告与报告原文核对。

## License

MIT。见 [LICENSE](LICENSE)。
