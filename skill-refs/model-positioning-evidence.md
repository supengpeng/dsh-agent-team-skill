# 模型定位依据与不确定性

> 本文为 [模型路由 skill](../skills/model-routing.md) 的按需参考，不自动加载。记录为什么这样分工，不充当全能排行榜。
> **证据层次**：已读官文原文支持模型用途；官方检索摘要 / 同家族近似版本只支持候选定位；配置仅证明声明的路由、输入类型和 effort；本机效果必须另看实际任务记录。
> 本轮没有真实调用全部模型，没有吞吐、稳定性、配额独立性或第三方端点 fidelity 的实测数据。官网价格、1M 等规格和厂商长程案例不能直接变成本机免费入口的保证。
> 外部实测排序旁证见 [第三方月榜读取指南](model-benchmark-ranking.md)：它只提供候选排序参考，不替代本文的官文证据与本机验收。
> **白名单变更（2026-10-18）**：当前 30 条可委派入口，`cn-f` / `en-f` 两个 provider 已不存在。**S13（Muse Spark 1.3）已通过 `god` provider 回归白名单**（别名 MS）；S12（Muse Spark 1.2）与 S14（Space Bunny）退为历史依据，不是候选；S12 的 1.2 行可作 MS 的下界参考。Hy4 只剩 `hy-f/workbuddy/hy4-preview-f` 一条免费入口；GLM 付费入口从 `zcode/GLM-5.3` 换成 `codearts/glm-5.3`。最新入口表见 [路由参考](model-routing-guide.md) 第一节。

## 输入材料与理解修正

用户提供的能力推演附件，SHA256：`a087933865841c6dfce97058731ffb3348f3c81056d42dab9df0ce2956761958`。原文第一行明确按命名、厂商历史和后缀推演，没有提供对这组入口的实测方法或来源。

接受其中「按场景分工、同模型不同入口互补容量」的方向，但不直接采用星级与 T0 / T1 排名：

- qoder、cline-free、contributor、our-free-model 是供应 / 产品 / 分发线索，不证明另有代码特化权重、社区微调或更纯净的模型。
- Flash 不等于只适合闲聊 / 端侧；Preview 不等于能力弱；版本较旧也不等于只能做低风险短任务。
- 模型能力与运行时工具 / Swarm 能力分开。厂商产品会开多个 agent，不代表 DSH 成员获准嵌套委派。
- 同名入口可尝试补配额，但不能保证相同部署质量、量化、上下文上限或独立账号额度。
- 不拿官方自报不同 benchmark 的数字拼绝对排名，不把 Gemini 普通 Flash 当 Flash Cyber。

## S1

### DeepSeek V4.1 Flash

来源：[DeepSeek 官方发布说明](https://www.deepseek.com/en/news/deepseek-v4-1-flash/)，已读取原文。

原文确认新的 MoE / CED 架构、原生视觉、能力与推理效率改进，并说明 WorkBuddy / CodeBuddy 与 OpenCode 支持。原文不建立「数学或代码绝对第一」的对照结论。

定位：逻辑推演、算法核验、疑难调试与证据复核。这是有验证条件的本机分工，不能只凭答案看似严密就通过验收。保持长文与图像候选能力，不限制成只做摘要。本机入口自 2026-10-18 为三条免费域 D1 `workbuddy/deepseek-v4.1-flash`、D2 `deepseek-v4.1-flash-free`、D3 `opencode/deepseek-v4.1-flash-free`，加两条付费 DB `buddy/…`（x0.11）、DC `codearts/…`（x1.00）；**五条都是 1017 快照**，能力一律对榜行 `DeepSeek V4.1 Flash 1017 (max)`（2026-10 code_v3：Science 13/B、Rust 12/B、Metal 14/C+、Animation 17/B、Simple 10/B+，iOS+Server 与 Data Process 为 Skip / Pending；logic 65.54 / 59.46，白名单最高）。同模型多入口只补额度，不构成独立复审；排序口径见 [路由参考](model-routing-guide.md) 第二节。

**三源张力（必须有意识地处理）**：用户实测（2026-10-18）认为 DS 1017 **编码≈Opus 5.5、前端≈GPT-6 Astra**，而 llm_benchmark 的 `code_v3` 2026-10 里这一行 `Simple` 只有 10/B+（Opus 5.5 是 Pass、GPT-6 Astra 是 5/A+）、`Animation` 17/B（Opus 4/A+）、`iOS+Server` 直接 Skip；BenchLM 只给它 coding 18 / 60.72（参照 Opus coding 2 / 82.62、Astra coding 4 / 75.46）。两者并不必然矛盾：`code_v3` 的题列里**没有前端 UI 题**，BenchLM 的 `deepseek-v4-1-flash` 单行**无法区分 1017 快照**（`-1017` slug 404）。因此前端 / UI 与真实项目编码以用户实测为准并 D 系前置，榜上题列只在它实际覆盖的形态（动画、Metal、科学计算、Rust、logic）里作证据。

## S2

### GLM-5.3

来源：[Z.ai 官方发布说明](https://z.ai/blog/glm-5.3)，已读取原文。

重点是复杂编码、真实工程环境、长程任务和 agent，远不止中文知识问答。本文不复制其 SOTA 宣传为跨榜绝对排名，也不将官方安全研究成绩当任何任务的授权。

定位：复杂工程实现、跨文件修复、测试设计与代码审查。2026-10-17 当前免费入口已声明 text,image；旧「仅文本」结论失效。图像任务仍须核对实际附件链路与端到端识图验收，声明不能证明质量。

## S3

### GLM-5.3-Flash

来源：[Z.ai Flash 官方发布说明](https://z.ai/blog/glm-5.3-flash)，已读取原文。

官方明确它是 GLM-5 系列首个原生多模态模型，从新训练的 base model 出发；同时强调编码 / agent 与效率，不是简单缩小旗舰后的聊天版。

定位：日常开发、视觉编程、UI 实现与截图反馈修正，这是基于其多模态与编码能力的本机分工。G → GF 是跨模型切换，不应一概写成能力降级；纯文本关键工程默认仍可选 G，但 GF 经本机验收后可承担实质编码。

## S4

### Kimi K3

来源：[Kimi K3 官方技术博客](https://www.kimi.ai/blog/kimi-k3)，已读取原文。

官方用途覆盖长程编码、知识工作、原生视觉、研究与复杂仓库，不仅「长文本阅读」。官网规格和案例只描述官方模型 / 产品环境，不等于当前反代容量与连续运行保障。

重要限制：官方指出保留历史消息的 harness 兼容敏感，跨模型中途切换可能使生成不稳定；还承认过度主动，可能替用户做未预期决策。

定位：大仓库、跨文档推理、复杂研究和长程工程。用独立新成员 / 子代理、明确 scope 与非目标；只交接任务事实与产物，不移植其他模型推理历史。DSH 兼容未验证时考虑 K26 / G。

## S5

### Kimi K2.6

来源：[Kimi K2.6 官方技术博客](https://www.kimi.ai/blog/kimi-k2-6)，已读取原文。

明确重点包括长程编码、多语言工程、前端 / DevOps / 性能优化、完整开发任务和 agent；不能因 K3 发布就把 K2.6 限制为长文摘要或简单备用。

官文展示的是官方模型及其评测环境，不能据此证明本机反代的部署质量与消息兼容。当前未对这些免费入口运行供应质量验证。

定位：持续编码、前端 / 全栈实施和 K3 容量替补。能否承担某复杂任务由本机工具、输入容量与验收决定，不按版本号直接宣布失败。

**外部实测（2026-10-16，已推翻「无榜分」）**：BenchLM `kimi-2-6` overall 59 / 58.40、coding **58** / 43.15、agentic 59 / 44.31、knowledge 49 / 60.26（`established` / `supported`，族 3）。它比 K3（coding 22 / 58.94）低一档，也低于 K27 之外的多数白名单家族，因此只作**低价值替补**：K3 排队或限流、任务又要长上下文（256000）时才用，不要因为它「官方主打前端」就把它排到 K3 之前。

## S6

> 历史资料：Gemini 已不在当前子代理白名单，本节只保留能力依据，不构成候选或启用授权。

### Gemini 3.8 Flash

来源：[Google 官方发布说明](https://blog.google/innovation-and-ai/models-and-research/gemini-models/3-8-flash-and-3-8-flash-cyber/)，已读取原文；模型 API 页面本轮提取失败，未据其补充规格。

普通 Flash 强调长程编码、agent、多步骤专业推理；复杂任务可能多用 token / 多调工具，并非在所有场景都最快。Flash Cyber 是独立的受控部署变体，其安全专用成绩不能归给当前普通 Flash 入口。

定位：多模态材料理解、跨领域推理与独立工程复审。不只看图，不宣布综合能力断层第一；当前只通过已声明并验证的文本 / 图像通路使用。

## S7

### Step 5 Preview

来源：[StepFun 官方模型文档](https://platform.stepfun.ai/docs/en/guides/models/step-5-preview)，已读取原文。

明确支持软件工程、多步骤 agent、长文理解和专业知识工作，尤其金融；官网原生图像 / 视频能力及规格不能直接移植到本机子代理通路。

定位：跨文档研究、财报 / 专业报告、图表与文本结合的分析。必须附来源与计算依据；应用提供工具，模型本身不天然具有搜索或本地执行权限。

## S8

### Qwen3.8 Flash

来源：[Qoder 官方 Flash 说明](https://docs.qoder.com/events/flashoffer)，已读取原文。

明确是兼顾响应速度和费用的 Qwen 多模态模型，支持编码、长文、图像和工具调用；没有说明它是 qoder 专属代码微调权重。官方还提示活动高峰期响应可能变慢。

定位：明确规格的小中型实现、测试、开发脚本与自动化。不要只当机械检索，也不预设它是最强代码模型。该免费活动说明适用于 Qoder International，不能据此保证 qodercn 或本机反代的活动期限和额度。

## S9

### MiMo V2.6 Flash

来源：[MiMo 官方平台](https://mimo.mi.com/)，已读取原文；Xiaomi 研究主页亦确认 V2.6 系列。V2.6 研究文章本轮提取失败，不拿搜索摘要冒充完整技术报告。

平台对 Flash 的明确定义是高效推理、专业办公高频调用与规模化任务的均衡模型；不是附件所猜测的端侧小模型。

定位：批量抽取、归类、摘要、结构化处理、办公材料与常规自动化。保持一般编码候选，不直接承诺长工具循环可靠性、最快速度或原生音视频在 DSH 中可用。

## S10

### Hy4 Preview

来源：[Tencent Hunyuan 官方仓库](https://github.com/Tencent-Hunyuan/Hy4-preview)，已读取原文。

重点是软件工程、规划 / 调试 / 验证、办公分析、科研及专业工作；仅按「中文生态、均衡」定位低估其工程用途。

官方明确承认耗时推理和过度验证。厂商内部专家对比不代表独立全能排名，也不能直接当免费入口的速度 / 能力保证。

定位：复杂需求与工程分析、专业产物、独立复审；避免把高频小任务默认交给它。当前白名单只剩 `hy-f/workbuddy/hy4-preview-f`（显示免费，配置未声明输入列表，按文本使用）；`hy-f/buddy/hy4-preview-f`（x0.29 付费）仍在配置声明里但未列入白名单，不得指派。

## S11

### Hy3

来源：[Tencent Cloud 的 Hy3 preview 说明](https://www.tencentcloud.com/techpedia/144775)，已读取原文，**但它描述 preview；与本机 hy3 ID 的精确映射尚未确认**。

资料强调复杂推理、隐含规则 / 上下文学习、指令遵循、代码与 agent；同时提示工具错误恢复与推理参数敏感。因此不能把它仅视为弱旧版，也不能预设稳定。

候选定位：中文规则与约束抽取、需求核对、常规编码 / 文档的工程备用。小任务确认实际版本和工具输出，再提升任务范围；不继承 preview 的容量数字。

## S12

### Muse Spark 1.2（历史行，仍可作 1.3 的下界参考）

来源：[Meta 官方 Muse Code / Muse Spark 1.2 发布说明](https://research.meta.ai/blog/introducing-muse-code-and-muse-spark-1-2)，已读取原文。

明确是 coding-focused 更新，覆盖代码生成、复杂调试、仓库理解、长程工程与端到端开发，并与 Muse Code 共训练。这不是单纯代码补全模型。

定位：实质性代码修改、仓库调试与 1.3 容量替补。Muse Code 的历史压缩 / 后台 agent / toolset 适配不能直接认定 DSH 具备相同可靠性或授权。

## S13

### Muse Spark 1.3（已通过 god provider 回归白名单，别名 MS）

来源：[Meta 官方 Muse Spark 1.3 发布说明](https://research.meta.ai/blog/introducing-muse-spark-1-3)，本轮已读原文；[官方模型页](https://dev.meta.ai/models/muse-spark) 的全文提取仍失败。另已读取 [OpenRouter 模型 API 说明](https://openrouter.ai/meta/muse-spark-1.3)，确认该服务声明工具调用，但不是本机实测或完整性能评估。

当前入口：`god/cline/cline-free/muse-spark-1.3-contributor`，显示名「Muse Spark 1.3 · 免费 · cline」，contextWindow 1000000 / maxTokens 96000，**未声明 input 列表**（按文本用），reasoningEfforts off / low / medium / high；`god` 的 baseURL 同为 `http://127.0.0.1:18900/v1`。

外部证据：llm_benchmark `logic` 2026-10 有 `Muse Spark 1.3 (max)` 60.92 / 中位 47.26（gap 22.42%，测试成本 ¥57.67，排 G 62.55 之后、GF 58.93 之前）；`code_v3` 2026-10 **没有 1.3 行**，只有 1.2（Simple 26/C、iOS 11/B），只能作下界。BenchLM（2026-10-16）无 1.3 榜分（`muse-spark-1-3` / `-contributor` 均 404 `current_ranking_not_found`），`muse-spark-1-2` overall 25 / coding 40 同样只作下界。

定位：**有界编码 / 逻辑推理候选**，logic 处于白名单中上（G 之后、GF 之前），编码只能按 1.2 下界推断，未验收前不进入主力。官方原文支持 agentic workflow / coding 方向；cline-free / contributor 是入口供应标识，不能凭前缀推断 Cline 特化权重。官方发布的 `max` 不等于当前免费入口支持；[OMP 的订阅范围问题记录](https://github.com/can1357/oh-my-pi/issues/11788) 已读原文，说明 contributor 的 `max` 特例针对 Muse Code 订阅，不能移植到 Cline / Our Free Model。旧 U13a / U13b 条目作废，新入口必须单独做工具调用、文件编辑、测试与输出边界验收。

## S14

### Space Bunny（历史，已移出白名单）

**核对日期：2026-10-10；可读资料主要覆盖 2026-09-23 至 2026-10-06 的预览期。** 新定位为「有界编码与测试候选备用」，不再只作身份探索，也不是极速主力或关键终审。没有对本机入口运行模型实测。

| 来源 / 已读程度 | 可支持的结论 | 不能推出的结论 |
|---|---|---|
| [OpenCode Zen 官方文档](https://opencode.ai/docs/zen/)，已读全文 | 列出 `space-bunny-free`，说明匿名 stealth、有限免费期和该服务的零留存 / 不训练政策 | 不证明本机 `our-free-model/space-bunny-free` 的实际映射、可用性和隐私政策 |
| [AI Coding Daily 编码测评](https://aicodingdaily.com/model/space-bunny)，已读全文；2026-09-30，OpenCode / Max | 57.19/80，平均每任务 19 分 19 秒；实质编码有可用性信号，耗时需关注 | 这是模型 + harness 组合和有限任务，不能宣布胜过 Kimi / GLM，亦不能给本机启用 `max` |
| [正面社区讨论](https://www.reddit.com/r/opencode/comments/1wqi4a8/my_honest_opinion_about_spacebunny/)，仅检索摘要 | 有用户反馈补出其他模型漏掉的 stubs，值得用真实任务验证完整性 | 个例不证明全仓 Bug 发现率、稳定性或整体胜率 |
| [冗长 / 过度思考讨论](https://www.reddit.com/r/opencode/comments/1woi31e/space_bunny_alpha_is_too_verbose_isnt/)，仅检索摘要 | 存在 verbosity / overthinking 抱怨，任务须限定输出和循环 | 不是统计性缺陷率；正文 / 评论完整语境未核实 |
| [与 Muse 的对照讨论](https://www.reddit.com/r/opencode/comments/1wwmkuj/space_bunny_vs_muse_spark_13/)，仅检索摘要 | 有用户称 Space Bunny 未完成所给任务，反馈并非一致好评 | 无法统一任务、挡位和 harness，不用于绝对比较 |
| [独立资料站](https://spacebunnyalpha.com/)，已读全文；访问说明截至 2026-10-06 | 提供长文 / 工具测试线索；报告 OpenRouter 预览结束、OpenCode 延长免费期 | 非官方站、身份推测反复变化；不把 GPT / MiniMax / DeepSeek 猜测写成事实，也不承诺免费终止日期 |

Reddit 正文、多次 JSON / old 页面尝试均未提取成功，因此社区讨论明确按弱证据使用。独立测评的方法文章本轮提取失败，仅引用模型页已明确说明的模型 / harness 配置与结果，不声称复现或验证其评分。

**分派办法**：可给非关键、边界明确且能跑测试的局部实现、局部重构、测试补充或代码完整性检查；它不是额外强制的一路调用。首个真实任务先确认本机文件 / 工具与输出契约，通过后才扩大范围。限制输出，重复读取或无新证据的工具循环应停止上报；保留独立复核，不做唯一关键审查者。

**入口限制**：匿名模型身份未确认；本机预算映射、`image` 声明与离线传图通路已核对，但没有做模型推理或识图验收。允许公开 / 脱敏图片，不据声明承担唯一视觉审查。调用标签为 `low / medium / high`，实际发送 `light / balanced / deep`，控制思考与回答共享的输出预算，不是原生思考强度；没有关闭档。当前总上限下对应 2048 / 8192 / 32768 tokens。编码从 `medium` 起，截断先查预算，再考虑 `high`；不照搬外站 `max / xhigh`、1M 限长、音视频或永久免费保证。详细输入与档位边界见 [配置审计](model-input-audit.md)，完整入口见 [路由参考](model-routing-guide.md)。

**隐私限制**：OpenCode 的零留存政策仅覆盖其确认的供应链，不能移植给本地反代。匿名或 `contributor` 入口先核对实际留存 / 训练政策，未核实时只给公开或脱敏材料，不传密钥、个人数据和私有源码。

## S15

### Qwen3.7 Plus / Qwen3.6 Plus

依据：[本机 profile 配置](../profiles/desktop/cordis.patch.yml) 中 bailian-he 的 Coding 端点、模型声明与会话白名单，以及已读原文的 [阿里云深度思考说明](https://help.aliyun.com/zh/model-studio/deep-thinking)、[官方 Chat 参数范围](https://www.alibabacloud.com/help/en/model-studio/qwen-api-via-openai-chat-completions) 和 [Coding Plan 图片支持说明](https://help.aliyun.com/zh/model-studio/coding-plan)。官方明确将这两个 Plus 模型列为默认开启、可关闭的混合思考模型，开关使用 `enable_thinking`；Chat 文档的原生 `reasoning_effort` 支持清单未列它们，不把本机旧 `low / medium / high` 表当成三种原生强度。本机校准为 `off / high` 两态，序列化为 `false / true`，不另发 effort 参数；补齐 `image` 声明，离线保留用户与工具返回图。未做付费端点生成 / 速度 / 识图实测。

定位：3.7 是独立于本地免费反代的套餐备用执行位，3.6 只补模型级故障。Coding 套餐不是免费入口；两者同套餐总额度耗尽时不能靠换模型恢复。

## S16

### 当前付费替补与相关入口（2026-10-18）

依据仅为 [当前 profile](../profiles/desktop/cordis.patch.yml) 与本次离线核对，没有新性能评测或在线生成。

- DB：`deepseek-f/buddy/deepseek-v4.1-flash`（x0.11），声明 text,image、off / low / high / max，替换旧 Raccoon 候选。沿用 DeepSeek 的任务候选定位，不沿用其他通道的真实梯度、额度或速度结论。
- DC：`deepseek-f/codearts/deepseek-v4.1-flash`（x1.00），声明 text,image、off / low / high / max，但输出上限只有 64000；历史 65537 触发 502 的记录仍按保守上限使用。
- GX：`glm-f/codearts/glm-5.3`（x1.25，显示「华为智能」），声明**仅 text**、low / high / max、1000000 / 128000。它取代旧的 `zcode/GLM-5.3`（仍声明但未列入白名单），不继承旧入口的识图结论，也不接收看图任务。
- K31：`kimi-f/buddy/kimi-k3-1`，**即 K3 的 buddy 付费渠道（用户确认，同模型）**。配置声明 1000000 / 32000、text,image、low / high / max；能力与 K3 相同，差别只在计费（x1.62）与输出上限（32000，K3 是 128000），长输出任务回到 K3。
- K28：`kimi-f/buddy/kimi-k2.8-preview`，声明 1000000 / 64000、**仅 text**、off / high。Preview 不表示强或弱；两处榜都无分。**用户已于 2026-10-18 决定取消该入口**，取消后不作候选，也不能派看图任务。
- K27：`buddy/kimi-k2.7`（x0.57，256000 / 32000），BenchLM 已给分但很弱（overall 100 / coding 62 / 41.79）。**用户已于 2026-10-18 决定取消**；保留记录只为与历史派活对账。
- GFX：`glm-f/trae/glm-5.3-flashx`（x0.31，仅 text，1000000 / 64000），两处榜都没有行。定位来自用户实测（2026-10-18）：**GF 的快速版，能力略高于 GF，速度与 DS V4.1 Flash 1017 相当**；只在明显赶进度、愿意用小额计费换速度时调用，需要推理深度或看图仍走 K3 / D / G 系。
- MM：`minimax-f/minimax/MiniMax-M3`，显示名只有「M3」，无倍率标记，按付费待授权处理；M3.1-Flash-Preview 虽在配置里声明，但未列入白名单。

配置获准与本会话政策分开，新增入口若仍被本会话拒绝就等待生效或选择已获准兼容候选；不反复派发验证。全部付费候选还须满足费用授权与路由预算。

## S17

### BenchLM 旁证（2026-10-16，第二数据源）

来源：BenchLM Data REST API，`BenchAlign v5.8`（2026-09-30），`dataAsOf: 2026-10-16`；读法见 [第三方月榜](model-benchmark-ranking.md) 第七节。它是聚合榜，只作家族级位置旁证，不替代 `code_v3` 的任务级结论。

| 白名单家族 | BenchLM 名次 / 分数（overall / coding / agentic） | 置信 |
|---|---|---|
| K3 / K31 | 18 / 69.86；22 / 58.94；**10 / 69.37** | established / supported |
| QX | 19 / 69.65；34 / 53.57；14 / 67.51 | established / supported |
| S1 / S2 | 20 / 69.45；**20 / 60.51**；18 / 65.31 | developing / estimated |
| G / GX | 26 / 67.87；28 / 56.40；**8 / 70.23** | established / supported |
| D 系 | 35 / 65.59；**18 / 60.72**；24 / 61.91 | established / supported |
| M1 / M2 | 38 / 64.50；**21 / 60.15**；38 / 56.13 | developing / estimated |
| Q1 / Q2（疑） | 43 / 62.80；41 / 51.60；17 / 65.55 | preliminary / estimated |
| H42 | 51 / 61.25；27 / 56.67；40 / 55.80 | benchmark-only / estimated，族 10 |
| GF 系 | 58 / 58.51；46 / 48.31；36 / 57.20 | developing / estimated |
| MM | 68 / 54.89；74 / 36.39；68 / 39.29 | established / supported |
| C36 / C37 | 79 / 53.78；66 / 39.96 ↔ 80 / 53.75；65 / 40.25 | established / supported（旧代） |
| H3x | 90 / 52.47；47 / 47.94；73 / 37.19 | developing / estimated |
| K27 | 100 / 49.51；62 / 41.79；69 / 38.53 | developing / supported |
| K26 | 59 / 58.40；58 / 43.15；59 / 44.31 | established / supported，族 3 |
| MS | 无榜分（404）；同族 `muse-spark-1-2` overall 25 / coding 40 只作下界 | — |

参照锚点：`claude-opus-5-5` overall 1 / 86.54、coding 2 / 82.62、agentic 1 / 90.31（`developing` / supported）；`gpt-6-astra` overall 3 / 83.87、coding 4 / 75.46、agentic 7 / 74.80（`established` / supported）。

至今零榜外分：**MS（Muse Spark 1.3）** 在 BenchLM 三条 slug 全 404，llm_benchmark 编码榜也无行（只有 logic 60.92 / 47.26 可引）；**GFX 两处都无分**，只有用户实测定位（GF 快速版、能力略高、速度≈DS V4.1 Flash 1017、赶进度场景）；**K27 / K28 用户已于 2026-10-18 决定取消**，不再参与排序。

低置信标注：`developing` / `preliminary` / `benchmark-only` 只影响排序置信，不改变「配置允许 ≠ 已验收」的结论；与 `code_v3` 冲突时以任务级近期数据为准。`mimo-v2-6-pro`（overall 12）是另一档，不能给 M1/M2 背书。DS 系的编码 / 前端以用户实测（≈Opus 5.5 / ≈GPT-6 Astra）为准——BenchLM 的 `deepseek-v4-1-flash` 单行无法区分 1017 快照（`-1017` 404），其 coding 18 是旧快照口径。

## 实测后如何调整

不为填排行榜空跑模型。遇到真实任务时记录模型 / 入口、任务类别、effort、首个可用结果的耗时、调用失败类型、是否通过验收，以及是否越界 / 循环 / 重复工具调用。

比较时采用相近输入与相同验收；区分模型能力不足、harness 不兼容、端点限长与供应限额。关键任务仍按质量门复核，不用单次成功宣布全能冠军。
