# 第三方月榜读取指南（llm2014/llm_benchmark）

> 本文为 [模型路由 skill](../skills/model-routing.md) 的按需参考，不自动加载。只回答「去哪读、怎么读、读到什么算什么」，不复制榜单结论。

## 一、定位与边界

- 来源：个人滚动题库的公开测评 `https://github.com/llm2014/llm_benchmark`，月榜托管在 GitHub Pages。作者自述「不够权威、不够全面、题库 28 题 / 270 用例以内、题目每月滚动更新」。
- 采分：每题满分 10 分，要求推导过程正确（猜对不得分）、严格符合题面（多余解释 / 违规编程记 0 分）；每题测 3 遍取最高分；超长思考扣分。
- 环境：优先 OpenRouter / Zenmux 中转并指定官方渠道供应商，模型侧统一参数。
- 因此：**只作候选模型的外部排序旁证**。它不能证明本机反代入口的质量、模态、上下文上限或配额，也不替代 `list_subagent_models` 预检、profile 白名单、凭据与会话策略（见 skill 第二节）。
- 月度波动：题目每月增减会带来 ±3 分左右正常抖动，跨月比较只看趋势，不当精确名次。

## 二、入口与直接取数

榜单页（SPA，`#` 后是前端路由）：

```
https://llm2014.github.io/llm_benchmark/#category=<类目>&dataset=<类目>|<YYYY-MM>|<表序号>
```

用户常引用的编码榜 2026-10 表 0 即 `#category=code_v3&dataset=code_v3%7C2026-10%7C0`（`%7C` 是 `|`）。

**坑：** 页面数据是运行时用相对路径 `data/...` 拉取的，抓 HTML 只能拿到总览（默认落到 Logic 类目），`#` 片段不会发给服务器。要拿指定类目 / 月份，必须直取数据文件。

数据索引（权威清单，含 `category` / `reportDate` / `tableIndex` / `title` / `csv` / `insights`）：

```
https://llm2014.github.io/llm_benchmark/data/datasets.json
```

月榜 CSV（推荐 raw 域名，纯文本直出）：

```
https://raw.githubusercontent.com/llm2014/llm_benchmark/main/docs/data/<类目>/<YYYY-MM>.csv
```

站点同路径 `https://llm2014.github.io/llm_benchmark/data/<类目>/<YYYY-MM>.csv` 语义相同，但网页抽取通道对 CSV 常返回 422，抓取失败时换 raw 域名，不要判定为「文件不存在」。

月内观察注释（可选）：

```
https://raw.githubusercontent.com/llm2014/llm_benchmark/main/docs/data/<类目>/<YYYY-MM>.insights.json
```

例：`docs/data/code_v3/2026-10.csv` + `2026-10.insights.json`。

### 本地存档（离线兜底）

本机桌面留有两份整页存档，用于网络取不到或只想快速看渲染结果时：

```
C:\Users\Fish\Desktop\致知｜大模型智力长期追踪榜单.html        （61733 字节，主档）
C:\Users\Fish\Desktop\致知｜大模型智力长期追踪榜单t.html       （50277 字节，另一份）
C:\Users\Fish\Desktop\致知｜大模型智力长期追踪榜单_files\      （网页资源目录）
```

- 存档日期 2026-10-18 18:06，内容固定在当日渲染的那一页：`#category=code_v3&dataset=code_v3|2026-10|0`，共 20 行模型、7 个题列。
- 读法：`Get-Content <文件> -Raw`，去掉 `<script>` / `<style>` 后按标签切词即可还原成行；也可直接在浏览器打开看表格。
- 边界：它是**静态快照**，只有 code_v3 2026-10 这一页的 7 个题列 + `Unprompted` / `Scaffold` / `Think`；没有 `insights.json` 点评，也没有其他类目与月份。要最新数据或换类目仍走上面的 `datasets.json` + raw CSV。

### 类目

| 类目 | 覆盖 | 现有数据段 |
|---|---|---|
| `code_v3` | 编码 / 应用工程（当前主编码榜） | 2026-01、2026-05、2026-07 起连续至最新 |
| `code` | 旧编码榜（多轮 / 首轮总分） | 2025-07 起，2026-02 后停止更新；另有 `<YYYY-MM>-lang.csv` 分语言平均成绩 |
| `logic` | 逻辑 / 数学 / 直觉综合 | 2024-04 起连续至最新 |
| `vision` | 视觉 | 2025-05 起若干月 |

### 已知数据口径问题

- `datasets.json` 里 `code_v3` 的 `reportDate` 标签与 `csv` 文件名不完全对应（如 `reportDate: 2026-04` 指向 `data/code_v3/2026-01.csv`）。**一律以 `csv` 字段为准，不要用 `reportDate` 拼文件名。**
- `insights.json` 的 `datasetKey` 可能与文件名不一致（2026-10 文件内写的是 `code_v3|2026-08|0`），按 `rowId` + `taskId` 读，别拿 `datasetKey` 当月份依据。
- 部分行整行字段被双引号包裹（含逗号的模型名等），用标准 CSV 解析器，别按逗号 `split`。

## 三、列语义

### code_v3（编码榜）

`模型, Simple Model(H), iOS+Server(I), Animation(J), Data Process(K), Metal(L), Science(M), Rust App(N), Unprompted, Scaffold, Think`

- 单元格取值：`Pass` / `Pending`（未测完）/ `Skip`（跳过）/ `Failed`，或 `<名次>/<等级>(<耗时秒>)`（例 `4/A+(341.72)`、`10/B(5.87)`）。
- 名次是**跨模型该题排名**（越小越好），等级是质量档位，括号内是该题耗时。
- `Unprompted`：需要额外提示才完成的次数（0 最好）；`Scaffold`：评测使用的脚手架（Claude Code / Codex CLI / DSH / OpenCode CLI 等），跨模型比较时注意脚手架并不统一；`Think`：是否推理模式。
- 用法：**看同一列（同一题）内的名次与等级**，不要跨列横向比数字；`Skip` / `Pending` 不代表能力差，只代表该题没测。

### code（旧编码榜）

`模型, 多轮总分, 首轮总分, 总轮数, 最终不可用, 测试成本(元), 平均耗时(秒), Token, 价格(元/百万), 发布时间, Think`

### logic / vision（分数榜）

`模型, 极限分数(Max), 中位分数(Median), 中位差距(Gap), 变更(Changed), 平均耗时(秒), Token, 测试成本(元), 价格(元/百万), 发布时间, Think`

- 排序看 `极限分数`（3 次取最高）与 `中位分数`（稳定性）；`中位差距 = (Max − Median) / Max`，越大越不稳。
- `变更`：相对上一期同月榜的变化，`-` 表示无可比数据。
- 成本列是「元」（CNY），页面按 6.9 折算 USD；与本机计费口径无关。

## 四、读取步骤（用于候选排序）

1. 定类目：编码 / 工程 / 工具应用 → `code_v3`；逻辑推演 → `logic`；识图 → `vision`。
2. 从 `datasets.json` 选该 `category` 下最新月份、`tableIndex: 0` 的月榜（或直接取已知最新 `<YYYY-MM>.csv`）。
3. 只抽与目标入口匹配的行：按家族名前缀对齐（如 `GLM-5.3`、`GLM-5.3-Flash`、`Kimi-K3`、`DeepSeek V4.1 Flash 1017`、`Qwen3.8-Max` / `Qwen3.8-Flash`、`Step 5 Preview`、`Hy3`、`MiniMax-M3`、`MiMo-V2.6`），同家族不同推理档（`max` / `xhigh` / `high`）是不同行；同家族还有快照行时（如 DeepSeek 的 `1017`），按白名单实际快照取，不取更旧的版本行；编码榜没有的行先去 logic 榜看，两榜都缺就标注「未上榜」。
4. 交叉本机条件再排序：profile 白名单 → provider 启用与凭据 → 输入模态 → 上下文 / 输出规模 → 费用与配额（免费优先，见 skill 第三节）。
5. 记录引用口径：类目 + 月份 + 行名 + 关键数字，例如「code_v3 2026-10：DeepSeek V4.1 Flash 1017 (max) 在 iOS+Server(I) 为 Skip、Animation(J) 为 17/B」。旧月份必须注明，别把历史榜当现状。

## 五、白名单 ID ↔ 榜行对照

榜单用官方模型名，本机别名与入口见 [路由参考](model-routing-guide.md) 第一节。**路由 skill 的能力排序就是按这张对照表来的**，所以对照必须精确到行，不能只对到家族：`code_v3` 的七个题列（Simple / iOS+Server / Animation / Data Process / Metal / Science / Rust App）就是「特化能力」证据，`logic` 补推理维度。**llm_benchmark 没有行的家族，用 BenchLM（第七节）补位**。

| 白名单家族 | llm_benchmark 榜行 | 说明 / BenchLM 补位 |
|---|---|---|
| D1 / D2 / D3 / DB / DC | `DeepSeek V4.1 Flash 1017 (max)` | **白名单里 5 条 DeepSeek 全部是 1017 快照**（用户确认）。不要用 `DeepSeek V4.1 Flash (max)` 行——那是旧快照，编码更差、logic 只有 63.61/56.41；`DeepSeek V4 Pro`、`DeepSeek V4.1 Flash`（无思考档）都不对应本机入口。BenchLM：`deepseek-v4-1-flash` overall 35 / coding **18** / agentic 24，但**只有这一行、无法区分 1017 快照**（`deepseek-v4-1-flash-1017` 404）→ 其名次低于用户实测：2026-10-18 用户判断 DS 1017 **编码≈Opus 5.5、前端≈GPT-6 Astra**，前端 / 真实项目编码按 D 系前置 |
| K3、K31 | `Kimi-K3 (max)` | **K31 就是 K3**（用户确认）：`kimi-k3-1` 是同一模型在 `buddy/` 的付费渠道（x1.62），能力直接取 `Kimi-K3 (max)` 行，不算近似版本。差异只在计费与输出上限（K31 32000，K3 128000）。BenchLM：`kimi-k3` overall 18 / coding 22 / agentic **10** |
| K26 / K27 / K28 | 均无对应行 | K26 = `kimi-2-6`（2026-04-20）**现在有分了**（2026-10-16 实测）：overall 59 / coding **58** / agentic 59 / knowledge 49，`established`，但整体偏弱（K3 是 coding 22）；K27 = `kimi-k2-7-code`（2026-06-12）：overall 100 / coding 62 / agentic 69，更弱；**K28 `kimi-k2.8-preview` 不在 BenchLM 目录、也无 code_v3 行**；**K27 / K28 用户已决定从白名单取消（2026-10-18）**，保留行仅作历史参照 |
| G、GX | `GLM-5.3 (max)` | G = `z-ai/glm-5.3`（免费），GX = `codearts/glm-5.3`（x1.25）同模型另一渠道；两者都仅文本。BenchLM：`glm-5-3` overall 26 / coding 28 / agentic **8** |
| GF / GF2 / GF3 | `GLM-5.3-Flash (max)` | 三条是同一模型的免费入口。BenchLM：`glm-5-3-flash` overall 58 / coding 46 / agentic 36。`GLM-5.3-FlashX`（GFX）两处都无行，**没有榜分**；位置只来自用户实测（2026-10-18）：**GF 的快速版，能力略高于 GF，速度与 DS V4.1 Flash 1017 相当**，付费 x0.31，明显赶进度时用 |
| QX | `Qwen3.8-Max-0902 (max)` | 同家族的 `Qwen3.8-Max (xhigh)` 是 logic 榜另一行，编码榜用 `0902` 行。BenchLM：`qwen3-8-max` overall 19 / coding 34 / agentic 14 |
| Q1 / Q2 | `Qwen3.8-Flash (xhigh)`（仅 logic） | 编码榜未收录 Flash；`Qwen3.8-27B` 不属白名单。BenchLM 只有 `qwen3-8-flash-next`（2026-08-26，疑同族未证实）：overall 43 / coding 41 |
| S1 / S2 | `Step 5 Preview`（仅 logic） | 编码榜无 Step 行；logic 55.66/42.72。BenchLM：`step-5-preview` overall **20** / coding **20** / agentic 18，是本机少见的强旁证 |
| H31 / H32 | `Hy3 (high)` | 编码榜有 Hy3 行（Simple 10/B、iOS 20/C+、J/K Failed），logic 40.27/31.44；BenchLM：`hy3` overall 90 / coding 47 |
| H42（Hy4） | 无对应行 | llm_benchmark 两榜都没有 Hy4。BenchLM：`hy4-preview` overall 51 / coding 27 / agentic 40（`maturity: benchmark-only`、`estimated`，来源族 10），明确强于 Hy3 |
| MM | `MiniMax-M3` | 编码垫底（Simple 30/D、iOS 17/C+），logic 31.28/27.29；BenchLM：`minimax-m3` overall 68 / coding **74** |
| M1 / M2 | 无对应行（榜上是 `MiMo-V2.6-**Pro**`） | 不拿 Pro 背 Flash。BenchLM 有 Flash 本行：`mimo-v2-6-flash` overall 38 / coding **21**；Pro 行 overall 12 是另一档 |
| C36 / C37 | 无对应行 | 百炼套餐入口，llm_benchmark 两榜均未测；BenchLM：`qwen3-6-plus` 79/53.78、`qwen3-7-plus` 80/53.75（明显是 Qwen3.8 之前的旧代） |
| MS（Muse Spark 1.3） | 无对应行 | `code_v3` 只有 `Muse Spark 1.2 (xhigh)`（Simple 26/C、iOS 11/B），**没有 1.3**；`logic` 有 1.3：60.92 / 47.26（gap 22.42%，¥57.67/次，落在 G 62.55 之后、GF 58.93 之前）。BenchLM 无 1.3 榜分，同族 1.2 行 overall 25 / coding 40 只作下界 |

补充几条对照纪律：

- Muse 家族：`Muse Spark 1.2`、`Muse Wonder 1.0` 榜上仍有但不在白名单；**`Muse Spark 1.3` 已通过 `god` provider 回归白名单（别名 MS，免费 contributor 入口）**，能力取 `logic` 的 60.92 / 47.26，编码榜无行。
- 上榜但不在白名单、不得因分数好就派：`Opus 5.5`、`GPT-6 Astra`、`GPT-6.1 Sol`、`Fable 5` / `Fable 5.1`、`Gemini 4 Argon`、`Gemini 3.8 Flash`、`Grok 4.7`、`Haiku 5.5`、`Seed-2.1-Pro`、`GPT-5.6 Luna`、`Sonnet 5.5`。
- 推理档要一起对齐：榜上行是 `(max)` / `(xhigh)` / `(high)`，本机 effort 若只有 `low,high,max`，别把 `(xhigh)` 行的成绩当成 `high` 档的。
- 同一家族跨入口（免费 vs 付费、国内 vs 国际）共用一行官方成绩，但**真实额度、限速、路由质量仍按入口区分**；成绩相同不代表体验相同。

## 六、复查与新鲜度

- 总榜随新模型测试实时更新，每月底归档一个月榜；写文档引用时带上「截至 YYYY-MM」。
- 桌面存档固定为 2026-10-18 那一页，引用它要额外写明「本地存档快照」，不能当成线上最新；线上与存档冲突时以线上 CSV 为准。
- 同一模型跨月出现的分数差异优先解释为题目滚动更新，其次才是能力变化。
- 本机入口若与榜上模型名对不上（版本号 / 快照日期 / 推理档不同），按「近似版本」处理，并在结论里写明不确定性。

## 七、BenchLM Data API（第二数据源）

llm_benchmark 只覆盖自家题库；白名单里 Step 5 / Hy4 / MiMo / K2.x / Qwen3.6·3.7 Plus 这类没有 `code_v3` 行的入口，用它补位。它是聚合榜（141 个 Arena 榜 + Epoch AI + 厂商模型卡表格），不是单一题库，所以只能当**家族级位置旁证**，不能替代 `code_v3` 的任务级结论。

**默认不启用**：本节与 [BenchLM 查询方法](benchlm-query.md) 只在更新路由技能 / 刷新排名时使用，日常选型不查。取数配方（凭据位置、Node 通道、参数、额度、错误与写入规范）全部收在 `benchlm-query.md`，本文件只保留口径与对照表。

- 入口：REST `https://data.benchlm.ai/v1`；MCP `https://data.benchlm.ai/mcp`（Streamable HTTP）；头 `Authorization: Bearer <BENCHLM_DATA_KEY>`。密钥存 DSH 凭据库 `C:\Users\fish\.dsh\.credentials.yaml` 的 `refs.BENCHLM_DATA_KEY`，不写进文档。
- 本机通道：直连 TLS 不通、`web_fetch` 带不了请求头；唯一可用姿势是 Node 24 的 `fetch`，脚本与读密钥方法见 [benchlm-query.md](benchlm-query.md) 第二、三节。
- 额度：Free 250 次成功读取/月、30 请求/分；`/v1/usage` 与 coverage 不扣额度，一次 `limit=100` 的分页算 1 次。
- 常用查询：`/v1/rankings/current?surface=overall|coding|agentic|knowledge&limit=100&offset=N`（`limit` 上限 100，单 surface 最多 220 行）；`/v1/models?limit=100&offset=N`（954 条，**不支持 `search`**，只能分页自己筛）；`/v1/models/{slug}/rankings/current`（单个模型四个 surface 一次给全）。
- 行字段：`rank`、`modelKey` / `slug`、`name`、`creator`、`score`、`evidenceStatus`（supported / estimated）、`maturity`（established / developing / preliminary / **benchmark-only**）、`sourceFamilyCount`、`scoreInterval90Lower/Upper`。`estimated` + `preliminary` 的低置信行要显式标注。
- 口径：BenchAlign v5.8（2026-09-30），`dataAsOf: 2026-10-16`；网站自述分类传播不确定性未验证、未来真值未验证，`orderingMeaning` 只是点估计排序。
- 别名不是 BenchLM ID：查表用 slug（见下表），例如 K3 → `kimi-k3`、H42 → `hy4-preview`。

白名单家族在 BenchLM 的位置（2026-10-16，名次 / 分数）：

| 白名单 | BenchLM slug | overall | coding | agentic | knowledge | 置信 |
|---|---|---|---|---|---|---|
| K3 / K31 | `kimi-k3` | 18 / 69.86 | 22 / 58.94 | **10 / 69.37** | 21 / 68.70 | established / supported |
| QX | `qwen3-8-max` | 19 / 69.65 | 34 / 53.57 | 14 / 67.51 | 24 / 68.13 | established / supported |
| S1 / S2 | `step-5-preview` | **20 / 69.45** | **20 / 60.51** | 18 / 65.31 | 22 / 68.69 | developing / estimated |
| G / GX | `glm-5-3` | 26 / 67.87 | 28 / 56.40 | **8 / 70.23** | 43 / 62.61 | established / supported |
| D 系 | `deepseek-v4-1-flash` | 35 / 65.59 | **18 / 60.72** | 24 / 61.91 | 33 / 65.28 | established / supported |
| M1 / M2 | `mimo-v2-6-flash` | 38 / 64.50 | **21 / 60.15** | 38 / 56.13 | 45 / 61.37 | developing / estimated |
| Q1 / Q2（疑） | `qwen3-8-flash-next` | 43 / 62.80 | 41 / 51.60 | 17 / 65.55 | 51 / 59.23 | preliminary / estimated |
| H42 | `hy4-preview` | 51 / 61.25 | 27 / 56.67 | 40 / 55.80 | 40 / 64.02 | **benchmark-only** / estimated，族 10 |
| GF 系 | `glm-5-3-flash` | 58 / 58.51 | 46 / 48.31 | 36 / 57.20 | 48 / 60.45 | developing / estimated |
| MM | `minimax-m3` | 68 / 54.89 | 74 / 36.39 | 68 / 39.29 | 82 / 49.57 | established / supported |
| C36 | `qwen3-6-plus` | 79 / 53.78 | 66 / 39.96 | 83 / 34.43 | 66 / 53.34 | established / supported |
| C37 | `qwen3-7-plus` | 80 / 53.75 | 65 / 40.25 | 80 / 35.14 | 65 / 53.59 | established / supported |
| H3x | `hy3` | 90 / 52.47 | 47 / 47.94 | 73 / 37.19 | 78 / 50.97 | developing / estimated |
| K27 | `kimi-k2-7-code` | 100 / 49.51 | 62 / 41.79 | 69 / 38.53 | 64 / 54.25 | developing / supported（**已决定取消**） |
| K26 | `kimi-2-6` | 59 / 58.40 | 58 / 43.15 | 59 / 44.31 | 49 / 60.26 | established / supported，族 3 |
| MS（Muse Spark 1.3） | 无榜分（`muse-spark-1-3-contributor` 404） | — | — | — | — | 只能取 llm_benchmark logic 60.92 / 47.26；同族 `muse-spark-1-2` overall 25 / coding 40 作下界 |

参照锚点（用于校准「名次意味着什么」）：`claude-opus-5-5` overall **1** / 86.54、coding **2** / 82.62、agentic 1 / 90.31（`developing` / supported）；`gpt-6-astra` overall **3** / 83.87、coding **4** / 75.46、agentic 7 / 74.80（`established` / supported）。用户判断 DS 1017 编码≈Opus、前端≈Astra，但 BenchLM 的 `deepseek-v4-1-flash` coding 只有 18 / 60.72——差距来自**该行不区分 1017 快照**，不是判断矛盾。

非白名单参照：`mimo-v2-6-pro` overall 12 / coding 13（比 Flash 强一档）；`kimi-2-6` overview 59 / coding 58（弱，仅作 K26 下限参考）；`kimi-k2-8-preview`、`glm-5-3-flashx` 不在目录。Muse 系：`muse-spark-1-2` 有分（overall 25 / coding 40），`muse-wonder-1-0` 与 1.3 两条 slug 均 404。

读法（与第四、五节的关系）：

1. 有 `code_v3` 行的家族，**`code_v3` 优先**——它测的就是我们的任务形态（Agentic / iOS+Server / 图形 / Metal），BenchLM 只用于交叉验证与「整体位置」。
2. 没有 `code_v3` 行的家族（S、H42、M1/M2、K26、C36/C37），用 BenchLM 的 **coding + agentic** 两名次排相对位置，再按 `developing` / `benchmark-only` / `estimated` 降置信；MS 没有 BenchLM 分，只用 `logic` 行；**GFX 两处都无分，只用用户实测定位（GF 快速版、速度≈DS 1017、赶进度场景）**；K27 / K28 已决定取消，不再列入排序。
3. 两源冲突时：任务级近期（`code_v3` / `logic` 2026-10）> BenchLM 综合分；结论里写明用了哪个源、哪一天，例如「BenchLM 2026-10-16：Step 5 Preview overall 20 / coding 20」。
