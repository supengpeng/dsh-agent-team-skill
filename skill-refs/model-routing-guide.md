# 模型路由按需参考

> 配置快照：2026-10-18，来源 `$DSH_HOME/profiles/desktop/cordis.patch.yml` 的 `subagent-model-selection-settings.allowedModels` 与 `llm-pi-ai.providers`。共 30 条（**K27 / K28 用户已决定取消，取消后 28 条**）：19 条显示免费、9 条按付费处理、2 条 Coding 套餐。价格标签只是显示名里的倍率标记，不是余额或可用性证明；此表记录配置声明，不宣称在线实测。

## 一、完整入口与配置声明

将完整路由按首个 `/` 拆成 provider 与完整 model。所有 `*-f` provider 都走本机反代 `http://127.0.0.1:18900/v1`（旧文档写的 8326 已作废）。免费入口的 ID 形如 `<模型>-free` 或 `opencode/<模型>-free`，已不再带 `our-free-model/` 前缀。实际指派仍核对本会话允许范围。

| 别名 | 完整路由 | 计费分类 | 输入 | effort 标签 | contextWindow | maxTokens |
|---|---|---|---|---|---|---:|---:|
| C36 | `bailian-he/qwen3.6-plus` | 套餐 | text,image | off,high | 1000000 | 65536 |
| C37 | `bailian-he/qwen3.7-plus` | 套餐 | text,image | off,high | 1000000 | 32768 |
| D1 | `deepseek-f/workbuddy/deepseek-v4.1-flash` | 免费声明 | text,image | off,low,high,max | 1000000 | 384000 |
| D2 | `deepseek-f/deepseek-v4.1-flash-free` | 免费声明 | text,image | off,low,high,max | 1048576 | 384000 |
| D3 | `deepseek-f/opencode/deepseek-v4.1-flash-free` | 免费声明 | text,image | off,low,high,max | 1048576 | 384000 |
| DB | `deepseek-f/buddy/deepseek-v4.1-flash` | 付费 x0.11 | text,image | off,low,high,max | 1000000 | 384000 |
| DC | `deepseek-f/codearts/deepseek-v4.1-flash` | 付费 x1.00 | text,image | off,low,high,max | 1000000 | 64000 |
| Q1 | `qwen-f/qodercn/qfmodel` | 免费声明 | text,image | off,low,medium,xhigh | 1000000 | 128000 |
| Q2 | `qwen-f/qoder/qfmodel` | 免费声明 | text,image | off,low,medium,xhigh | 1000000 | 128000 |
| QX | `qwen-f/qodercn/qmodel_38max` | 付费 x0.5 | text,image | off,low,medium,xhigh | 1000000 | 128000 |
| G | `glm-f/z-ai/glm-5.3` | 免费声明 | text | low,high,max | 1000000 | 128000 |
| GF | `glm-f/z-ai/glm-5.3-flash` | 免费声明 | text,image | low,high,max | 1000000 | 128000 |
| GF2 | `glm-f/glm-5.3-flash-free` | 免费声明 | text,image | low,high,max | 1000000 | 128000 |
| GF3 | `glm-f/opencode/glm-5.3-flash-free` | 免费声明 | text,image | low,high,max | 1000000 | 128000 |
| GFX | `glm-f/trae/glm-5.3-flashx` | 付费 x0.31（GF 的快速版：能力略高于 GF、速度≈DS V4.1 Flash 1017，明显赶进度时用） | text | low,high,max | 1000000 | 64000 |
| GX | `glm-f/codearts/glm-5.3` | 付费 x1.25 | text | low,high,max | 1000000 | 128000 |
| K3 | `kimi-f/moonshotai/kimi-k3` | 免费声明 | text,image | low,high,max | 1000000 | 128000 |
| K26 | `kimi-f/moonshotai/kimi-k2.6` | 免费声明 | text,image | off,high | 256000 | 64000 |
| K31 | `kimi-f/buddy/kimi-k3-1` | 付费 x1.62 | text,image | low,high,max | 1000000 | 32000 |
| K28 | `kimi-f/buddy/kimi-k2.8-preview` | 付费 x0.77；**用户已决定取消（2026-10-18）** | text | off,high | 1000000 | 64000 |
| K27 | `kimi-f/buddy/kimi-k2.7` | 付费 x0.57；**用户已决定取消（2026-10-18）** | text,image | high | 256000 | 32000 |
| M1 | `mimo-f/cline/cline-free/mimo-v2.6-flash` | 免费声明 | text,image | off,low,medium,high | 1000000 | 128000 |
| M2 | `mimo-f/mimo-v2.6-flash-free` | 免费声明 | text,image | off,low,medium,high | 1000000 | 128000 |
| MM | `minimax-f/minimax/MiniMax-M3` | 付费（无倍率标记） | text,image | off,high | 1000000 | 128000 |
| S1 | `step-f/buddy/step-5-preview` | 免费声明 | text,image | low,medium,high | 1000000 | 64000 |
| S2 | `step-f/workbuddy/step-5-preview` | 免费声明 | text,image | low,medium,high | 1000000 | 64000 |
| H42 | `hy-f/workbuddy/hy4-preview-f` | 免费声明 | 未声明输入列表，按 text | off,high | 1000000 | 64000 |
| H31 | `hy-f/buddy/hy3` | 免费声明 | text | low,high | 192000 | 64000 |
| H32 | `hy-f/workbuddy/hy3` | 免费声明 | text | low,high | 192000 | 64000 |
| MS | `god/cline/cline-free/muse-spark-1.3-contributor` | 免费声明 | 未声明输入列表，按 text | off,low,medium,high | 1000000 | 96000 |

付费显示倍率：DB x0.11、GFX x0.31、QX x0.5、K27 x0.57、K28 x0.77、DC x1.00、GX x1.25、K31 x1.62；MM 无倍率标记，不推断免费或更便宜。倍率仅供同计费口径下筛选，不替代实际价格与总成本。

**别按渠道前缀推断计费。** 同是 `buddy/`，`deepseek-v4.1-flash` 显示 x0.11 付费，而 `hy3` 显示 x免费；同是 `workbuddy/`，常见「x免费 · 腾讯国际」。以每条模型自己的显示名为准，前缀只说明中转渠道。

### 本轮变化（2026-10-18 校对）

- 白名单 32 → 29 条，随后同日晚间因 Muse Spark 1.3 加入变为 **30 条**。provider 从 11 个降到 9 个、再因 `god` 回到 10 个：**`cn-f`、`en-f` 已不存在**，所以旧的 S1 / S2 / S3（cn-f）、U13a / U13b / U12（en-f）、SB（en-f）全部失效。
- Step 迁到 `step-f`：只剩 `buddy/`、`workbuddy/` 两条免费入口，S3 取消。
- 免费 ID 去掉了 `our-free-model/` 前缀：D2 由 `our-free-model/deepseek-ai/deepseek-v4.1-flash` 变为 `deepseek-v4.1-flash-free`，GF2 / M2 同理；另有 `opencode/<模型>-free` 变体（D3、GF3）。
- DeepSeek 免费入口从 2 条变 3 条（D1 / D2 / D3），三条同模型不同额度域。
- GLM 免费入口从 2 条变 4 条（G / GF / GF2 / GF3）；`glm-f/zcode/GLM-5.3`（旧 GX）已不在白名单，`codearts/glm-5.3`（x1.25）取而代之。
- Hy：`buddy/hy4-preview-f`（x0.29 付费）仍在配置里声明，但**未列入白名单**，不要派；hy4 只剩 H42 一条免费入口，H31 / H32 仍是 hy3。
- Kimi K27 / K28 的输入声明收窄：K28 仅 text，K27 text,image 但输出上限只有 32000。
- MiniMax M3.1-Flash-Preview、`god` provider 下的 Claude / GPT / Gemini / Grok 系列，以及 `gpt`、`bailian`（非 he）provider 的模型都在配置里声明，但**均未列入白名单**，不是当前可委派入口。
- **新增 MS：Muse Spark 1.3 经 `god` provider 回到白名单**——`god` 的显示名是「我在佛前苦苦求了几千年」，baseURL 同为 `http://127.0.0.1:18900/v1`，该条为 Cline 免费 contributor 入口（无倍率标记）；`god` 下其余 Claude / GPT / Gemini / Grok 仍未列入白名单。
- 已用 `list_subagent_models` 复核目录：`step-f` 只列 2 条、`hy-f` 只列 3 条、`glm-f` 只列 6 条——**目录本身按白名单过滤**，未列入白名单的入口（`buddy/hy4-preview-f`、`zcode/GLM-5.3`、`MiniMax-M3.1-Flash-Preview` 等）不会出现在目录里，传了也会被会话策略拒绝。但 `god` 是例外：配置已列入白名单，当前会话仍只返回 9 个 provider、`provider:"god"` 直接报 `LLM provider "god" is not allowed for this Session` → **MS 要重开会话或等策略刷新后才能派**。

同模型免费替补：D1 → D2 → D3；Q1 → Q2；GF → GF2 → GF3；M1 → M2；S1 → S2；H31 → H32；H42 / MS 无同模型第二入口。独立额度均未保证。K3 → K26 是跨版本；G ↔ GF 是不同档；**K31 与 K3 是同模型的两个渠道**（不构成独立复审，也不是双份能力）；C37 → C36 属同套餐，不能补账号总额耗尽。

## 二、按能力与特化选型（2026-10 能力表）

依据：桌面存档《致知｜大模型智力长期追踪榜单》2026-10 月榜（`code_v3` 七题 + `logic`），快照 2026-10-18；**没有 `code_v3` 行的家族用 BenchLM（2026-10-16，BenchAlign v5.8）补位**，详见 [月榜指南](model-benchmark-ranking.md) 第七节。名次 = 跨模型该题排名（越小越好），等级 = 质量档，¥ = 该题测试成本。顺序是**排序旁证**，硬约束与预检仍在最前，费用在最后。

### 白名单家族的能力画像

| 家族 | 最强项 | 明显弱项 | logic Max / Median | 备注 |
|---|---|---|---|---|
| D 系（DeepSeek V4.1 Flash **1017**） | Science 13/B、Rust 12/B、Metal 14/C+、Animation 17/B；logic 白名单最高；**用户实测 2026-10-18：编码≈Opus 5.5、前端≈GPT-6 Astra** | iOS+Server **Skip**、Data Process **Pending**；Simple 仅 10/B+ | 65.54 / 59.46 | 白名单 5 条 DS 全是 1017 快照；单题 ¥5.9–12.3，最便宜；BenchLM coding 18 / overall 35（该行无法区分 1017 快照，低于用户实测） |
| K3 / K31（Kimi-K3） | Simple **6/A**、iOS+Server **5/A**（白名单最好） | Data Process 28/D+、Metal 25/D、Science 26/C+、Rust **Failed** | 64.22 / 56.73 | 单题成本高（¥29–383）；K31 = 同模型 buddy 付费渠道，输出上限 32000；BenchLM agentic 10 / overall 18 |
| G / GX（GLM-5.3） | Simple 8/B+、iOS+Server 7/A | Science **Failed**、Rust 34/C、Data Process 30/D、Metal 24/D | 62.55 / 51.15 | 仅文本；BenchLM **agentic 8**（白名单最好）/ coding 28 |
| GF / GF2 / GF3（GLM-5.3-Flash） | Simple 9/B、iOS+Server 13/B | J / K / L / M / N 全 Failed 或 Skip | 58.93 / 48.01 | logic 单次只 ¥3.83，只适合小步实现；BenchLM overall 58 |
| GFX（GLM-5.3-FlashX） | 两榜均无行 → 无榜分 | 用户实测（2026-10-18）：GF 的快速版，能力略高于 GF、**速度≈DS V4.1 Flash 1017** | 未上榜 | 付费 x0.31、仅文本；只在明显赶进度时用付费换速度，日常仍走免费 GF |
| QX（Qwen3.8-Max） | Simple 8/B、iOS+Server 7/A、Data Process **13**（本机最好名次） | Science 26/D、Metal Skip、Rust Skip | 54.70 / 48.71 | BenchLM overall 19 / agentic 14 |
| Q1 / Q2（Qwen3.8-Flash） | 编码榜未收录 | — | 53.95 / 41.00（中位差 24%） | 稳定性明显差于 QX；BenchLM 疑为 `qwen3-8-flash-next`（43/62.80，未证实） |
| S1 / S2（Step 5 Preview） | 编码榜未收录，但 BenchLM coding **20 / 60.51**、overall 20 / 69.45 | 低置信（developing / estimated） | 55.66 / 42.72（gap 23%） | 原先「只有逻辑榜证据」，现按 BenchLM 视为编码强候选 |
| H3x（Hy3） | Simple 10/B | iOS 20/C+、J / K Failed | 40.27 / 31.44 | 仅文本、192K；BenchLM overall 90 / coding 47，明显弱于同族 Hy4 |
| H42（Hy4） | BenchLM overall 51 / 61.25、coding 27 / 56.67 | llm_benchmark 未收录；`benchmark-only` 口径 | 未上榜 | 明确强于 Hy3；`estimated` + `benchmark-only`，先有界任务验收 |
| MM（MiniMax-M3） | Simple 30/D、iOS 17/C+ | 其余 Failed / Skip | 31.28 / 27.29 | 白名单垫底；BenchLM coding 74 更差 |
| M1 / M2（MiMo-V2.6-Flash） | BenchLM coding **21 / 60.15**、overall 38 / 64.50 | 低置信（developing / estimated）；llm_benchmark 未收录 | 未上榜 | 不拿 Pro（overall 12）背书；BenchLM 显示它在编码中段，不再只算「抽取类」 |
| C36 / C37 | BenchLM overall 79 / 53.78 与 80 / 53.75（Qwen3.6 / 3.7 Plus 旧代） | 编码、agentic 全面落后 Qwen3.8 线 | 未上榜 | 套餐入口，末位备用 |
| K27（Kimi K2.7 Code） | BenchLM coding 62 / 41.79 | overall 100，白名单垫底区 | 未上榜 | 旧代 Code 版本；**用户已决定取消（2026-10-18）** |
| MS（Muse Spark 1.3） | logic 60.92 / 中位 47.26（落在 G 与 GF 之间） | code_v3 无 1.3 行；同族 1.2 行 H 26/C、I 11/B 只能当下界 | 60.92 / 47.26（gap 22.42%，¥57.67/次） | 免费 Cline contributor 入口；输入未声明按 text；BenchLM 无 1.3 行（1.2 overall 25 / coding 40） |

### 按任务的首选顺序

从左优先；同一档看成本与上下文。

| 任务 | 顺序 |
|---|---|
| 单文件实现 / 小改动 / 快速脚本（Simple H） | K3 → G·GX → QX → GF 系 → D 系 → H3x → MM |
| iOS+Server 全栈联动（I） | K3 → G·GX → QX → GF 系 → MM → H3x；**D 系该题为 Skip，不派** |
| 动画 / 图形（J） | D 系 → G·GX → QX → K3 |
| 数据处理（K） | QX（名次 13/D）→ K3（28/D+）→ G·GX（30/D）；D 系该题为 Pending，按「无证据」处理不前置；整族都不强，先拆任务或本地脚本 |
| Metal / 渲染（L） | D 系唯一有可用名次（14/C+）；G、K3 均为 D 档，不优先 |
| 科学计算（M） | D 系（13/B）→ K3（26/C+）→ QX（26/D）；G 该题 Failed |
| Rust 应用（N） | D 系（12/B）→ G·GX（34/C）；K3 该题 Failed |
| 逻辑 / 数学 / 推理（logic） | D 系 65.54/59.46 → K3 64.22/56.73 → G·GX 62.55/51.15 → MS 60.92/47.26 → GF 系 58.93/48.01 → S1/S2 55.66/42.72 → QX 54.70/48.71 → Q1/Q2 53.95/41.00 → H3x 40.27/31.44 → MM 31.28/27.29 |
| 批量抽取 / 分类 / 摘要 | M1 / M2（BenchLM coding 21 / overall 38，先小样本验收）→ Q1 / Q2 → H3x |
| 截图 / 视觉 | GF 系、QX、Q1 / Q2、D 系、S1 / S2、K3、MM（都有 image 声明，仍需实测）；G / GX / GFX / H3x 不接；MS 未声明输入，按文本，不接图 |
| 前端 / UI 实现（用户实测） | D 系（1017：编码≈Opus 5.5、前端≈GPT-6 Astra）→ K3 → G·GX → QX → GF 系 → H42 → MS；榜上无前端题列，按用户判断 |
| 赶进度 / 速度优先（用户实测） | GFX（GF 的快速版：能力略高于 GF、速度≈DS V4.1 Flash 1017、付费 x0.31）→ GF / GF2 / GF3（免费替代）；只有明显赶进度时才用付费换速度 |
| 混合编码 / Agentic（BenchLM coding 旁证，不含前端） | K3（22）→ D 系（18）→ S1/S2（20）→ M1/M2（21）→ H42（27）→ G·GX（28）→ QX（34）→ GF 系（46）→ H3x（47）→ K26（58）→ C36/C37（65–66）→ MM（74）；名次为 BenchLM 2026-10-16 |
| 多步 Agentic / 工具链（BenchLM agentic） | G·GX（8）→ K3（10）→ QX（14）→ S1/S2（18）→ D 系（24）→ GF 系 / M1/M2（36–38）→ H42（40）→ MM（68）→ H3x（73） |
| 独立复审 | 按「与实现不同家族」选：实现走 K3 → 用 D 系 / G 复审；实现走 D 系 → 用 K3 / G 复审；同模型多入口不算独立 |

### 读表规则

- 名次是跨模型该题排名，不是全局百分比；`Failed` / `Skip` / `Pending` 一律算「没有证据」，不折算成低分。
- 名次与等级可能矛盾（K 题 QX 名次 13 但等级 D）：派活先排除 Failed / Skip，再比名次与等级。
- 单题成本可差两个量级（K3 的 K 题 ¥383 vs D 系 ¥12）；预算紧时按成本截断并说明原因。
- llm_benchmark 无对应行的家族里，K26（overall 59 / coding 58，偏弱）、H42（51/27）、M1/M2（38/21）、S1/S2（20/20）、C36/C37（79–80）已由 BenchLM 补旁证，MS 由 llm_benchmark logic 60.92 补旁证（见第七节），引用时写清源与日期。K31 例外——它就是 K3，直接取 K3 行，只是输出上限更小。
- **GFX 没有任何榜分**，只按用户实测定位（2026-10-18）：GF 的快速版，能力略高于 GF，速度与 DS V4.1 Flash 1017 相当；赶进度时用付费换速度，不用于需要推理深度或看图的任务（仅文本）。**K27 / K28 用户已决定取消（2026-10-18）**，不再作候选。
- BenchLM 的 DeepSeek 行无法区分 1017 快照（coding 18 / 60.72），DS 的编码与前端能力以用户实测为准（≈Opus 5.5 / ≈GPT-6 Astra）；榜上名次与用户实测冲突时，任务级实测优先。
- 能力证据分三层：配置声明 → 通道 / 适配器支持 → 真实任务验收。榜单属于第三层里的「外部旁证」，仍不等于本机验收；未做端到端识图只能写「声明支持」。定位细节见 [定位依据](model-positioning-evidence.md)。

## 三、兜底决策与共享预算

费用顺序统一为：免费首选 → 同模型免费替补 → 兼容免费模型 → 获准付费兜底。每任务每轮共享 **3 个不同完整路由**，含首选；每入口一次，换链、换成员、自动续轮不重置，只有用户批准新一轮才重置。阶段可跳过，不能为了走到末级突破预算。

| 失败类别 | 判断与下一步 | 禁止行为 |
|---|---|---|
| 配置与本会话政策不一致 | 记录 profile 已允许但本会话仍拒绝；选已获准兼容候选或等新会话 | 改别名 / provider 绕政策、反复试新 ID |
| 目录 / 策略拒绝，尚未启动 | 不算模型能力失败；记录本轮预检拒绝项 | 将目录列出当授权、未列出当唯一否定依据 |
| 鉴权 / 凭据 / provider 停用 | 按入口或账号范围停止排查；仅已独立就绪且获准候选可继续 | 将认证失败当付费授权、轮询共享账号 |
| 参数、effort、上下文或附件不兼容 | 纠正或拆分输入，验收不变；再次执行同路由先获新一轮许可 | 同参数重试、改模型掩盖输入错误 |
| 明确限额 / 429 | 按响应确定入口、账号或共享池，尊重 Retry-After，记录冷却 | 凭单条错误推断整个 provider 耗尽、无等待重试 |
| 单入口故障 / 超时 | 核对旧执行与外部副作用，停止后换兼容入口 | 有活跃旧执行仍派写任务 |
| 18900 反代整体故障 | 有连接 / 健康证据后排除同端点所有免费及付费 `*-f` 入口；获准 C37 / C36 可为不同端点备用 | 在同端点 provider 间空转；凭普通超时断定全故障 |
| 团队模型启动前报错 | 如 `guardedContinuable` 或 `Cannot read properties of undefined (reading 'call')`：保留任务，记录运行时故障；恢复宿主或经用户授权逐任务接管 | 当限额换遍模型、擅改插件、擅接实现 / 审查 |
| 测试失败、需求偏离、质量门失败 | 回调试 / repair；换模型也保留证据和验收 | 将质量失败标限额、新建任务洗掉失败 |
| 预算耗尽或无兼容获准入口 | 停止，报告已试路由、失败范围、恢复条件和剩余工作 | 续轮清零、降低验收、默默付费或自接管 |

预检拒绝不代表模型推理费用；仍记录拒绝项，不以「尚未调用」为由无限遍历。预检与实际执行各自有界，最多检查本轮三个目标；明确全局故障可立即停止。

付费条件：免费候选确实不可用或无法满足硬约束，且用户已授权对应模型 / 套餐 / 预算；否则先问。配置有付费入口不等于批准消费。按实际端点分故障域；敏感资料核对通道留存 / 训练政策。

安全交接：最终失败 / 已停止 → 收集关联作业 → 核对文件和外部副作用 → 保留任务 ID、依赖、恢复点与验收 → 新 owner 用新 attempt。生命周期见 [协作协议](subagent-driven-dev/protocol.md) 第五节。

## 四、实际指派检查单

1. 当前 profile 包含完整目标，provider 启用且凭据就绪；不输出密钥。
2. `list_subagent_models` 查询目录 / effort；本会话若尚未更新，以策略拒绝为边界，不伪称已生效。
3. 模态、容量、工具、数据政策与审查独立性可满足；只传必要材料。未列入白名单的条目（如 `buddy/hy4-preview-f`、`minimax/MiniMax-M3.1-Flash-Preview`）不派。选型顺序按第二节 2026-10 能力表；DeepSeek 一律对 `1017` 快照行。
4. provider / model 成对且遵守上层跨 provider 限制；省略是继承，只有当前路由获准且适配时才用继承。
5. effort 用目标真实支持值，不确定省略。表一仅声明，映射参见 [配置审计](model-input-audit.md)。
6. 记录首选、兼容替补、计费授权、已试路由与冷却范围，不空跑替补。
7. AgentTeams 先暂存批准；运行时换模型用兼容新成员安全重派，不手改运行数据。
8. 汇报原 / 实际路由、费用、限制、验证与未完成项；调用成功不等于验收通过。

## 五、维护与证据

白名单和字段以当前 profile 为源，本表只是快照。新增 / 删除 / 修改输入、effort、容量后同步第一节和任务候选。[配置审计](model-input-audit.md) 记录历史边界；[定位依据](model-positioning-evidence.md) 记录职责出处；需要外部实测排序旁证时读 [第三方月榜](model-benchmark-ranking.md)。**刷新排名 / 能力表时**按 [BenchLM 查询方法](benchlm-query.md) 取数（默认不启用，日常选型不查）；外部资料不证明本机通道在线。

2026-10-18 校对只做离线 YAML 解析与 30 条逐项核对（含当日晚间新增的 MS）：没有模型生成，没有验证余额、真实推理梯度、识图或新入口性能；`cn-f` / `en-f` 的消失来自 provider 列表与白名单同时缺项，不是本次改动造成。
