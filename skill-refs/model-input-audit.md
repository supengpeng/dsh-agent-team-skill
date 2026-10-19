# 模型输入、容量与档位审计

> 配置快照：2026-10-18。可委派入口与完整声明统一见 [路由参考](model-routing-guide.md) 第一节。本文件不是授权清单；历史实测、适配器菜单、配置声明与本次验证分开记录。

## 一、当前白名单

- 30 条（**其中 K27 / K28 用户已于 2026-10-18 决定取消，取消后为 28 条**）：19 条显示免费、9 条付费、2 条 Coding 套餐；24 条声明 text,image，4 条只声明 text（G、GX、H31、H32），另 2 条未声明输入列表（H42、MS），按 text 使用。配置声明计数不是识图实测。
- provider 共 10 个：`bailian-he`、`deepseek-f`、`kimi-f`、`qwen-f`、`glm-f`、`hy-f`、`minimax-f`、`mimo-f`、`step-f`、`god`（`god` 只放了 Muse Spark 1.3 一条，baseURL 同为 `http://127.0.0.1:18900/v1`，`apiKeyEnv: GOD_API_KEY`）。**`cn-f` / `en-f` 已不存在**，旧的 S1 / S2 / S3、U13a / U13b / U12、SB 随之失效。
- 免费入口 ID 不再带 `our-free-model/` 前缀：`deepseek-v4.1-flash-free`、`glm-5.3-flash-free`、`mimo-v2.6-flash-free`，另有 `opencode/<模型>-free` 变体。
- 有声明但未列入白名单、不会出现在 `list_subagent_models` 的入口（目录按白名单过滤）：`hy-f/buddy/hy4-preview-f`（x0.29）、`glm-f/zcode/GLM-5.3`、`glm-f/zcode/GLM-5.3-Flash`、`minimax/MiniMax-M3.1-Flash-Preview`，以及 `gpt`、`bailian` 两个 provider 下的全部模型、`god` 下除 `cline/cline-free/muse-spark-1.3-contributor` 以外的模型。本次已抽查 `step-f`（2 条）、`hy-f`（3 条）、`glm-f`（6 条）与白名单一致。
- K31 的 model 为 `buddy/kimi-k3-1`，**与 K3 是同一个模型**（用户确认），只是 buddy 付费渠道（x1.62）且输出上限只有 32000；K27（`buddy/kimi-k2.7`）与 K28（`buddy/kimi-k2.8-preview`）**用户已决定取消（2026-10-18）**，取消后下表相关行仅作历史记录。配置允许不等于本会话政策刷新：曾观察到新增 Kimi 目录查询仍被本会话拒绝，实际指派前核对。**2026-10-18 复测**：`list_subagent_models` 仍只返回 9 个 provider（无 `god`），`list_subagent_models({provider:"god"})` 直接报 `LLM provider "god" is not allowed for this Session` → MS 要重开会话（或等策略刷新）后才会出现在子代理目录。
- 白名单内 5 条 DeepSeek（`workbuddy/`、`buddy/`、`codearts/`、`-free`、`opencode/...-free`）**都是 1017 快照**，能力对照统一取榜行 `DeepSeek V4.1 Flash 1017 (max)`，不取 plain `DeepSeek V4.1 Flash` 行。能力排序见 [路由参考](model-routing-guide.md) 第二节。
- 不把历史主模型、余额、精确价格或全 profile 模型总数写作现状。

## 二、输入边界

图片任务须同时有 image 声明、实际图片块和识图验收；缺少声明时 pi-ai 可能替换为文本占位符，回答成功不等于看过图。当前声明 text,image 的 24 条含 DeepSeek 5 条、Qwen 3 条、GLM Flash 4 条、Kimi 5 条、MiMo 2 条、MiniMax 1 条、Step 2 条、百炼 2 条；本次没有在线识图实测。

只声明 text、不接看图任务：`glm-f/z-ai/glm-5.3`（G）、`glm-f/codearts/glm-5.3`（GX）、`glm-f/trae/glm-5.3-flashx`（GFX）、`hy-f/buddy/hy3` 与 `hy-f/workbuddy/hy3`。`hy-f/workbuddy/hy4-preview-f`（H42）与 `god/cline/cline-free/muse-spark-1.3-contributor`（MS）配置里都没有 input 列表，一律按文本使用。

本机反代附件读取器历史审计只发单张且不超过 8 MB 的图片；本次未复查插件版本，作为保守限制而非永久契约。官网音视频、工具或隐私能力不自动适用于反代。敏感资料核对实际端点留存政策；匿名 / contributor 未核实前仅给公开或脱敏材料。

## 三、容量与输出上限

逐入口 contextWindow / maxTokens 见路由参考；只用于截断估算，不是通道承诺。预留系统提示、工具返回和输出余量。

| 入口 | 当前配置：上下文 / 输出 | 边界 |
|---|---|---|
| D1 | 1000000 / 384000 | 配置声明 |
| D2 / D3 | 1048576 / 384000 | 同模型不同免费域，上限比 D1 略高；不证额度独立 |
| DB | 1000000 / 384000 | 付费 x0.11，不套 DC 的小输出上限 |
| DC | 1000000 / 64000 | 历史最小调用 65536 可接受、65537 返回 502 `The request param is invalid`；本次不复跑 |
| K31 | 1000000 / 32000 | 与 K3 同模型，但输出上限只有 K3 的四分之一；长输出任务回 K3 |
| K28 | 1000000 / 64000 | **已决定取消（2026-10-18）**；仅 text，历史记录 |
| K27 | 256000 / 32000 | **已决定取消（2026-10-18）**；上下文与输出都小于 K3，历史记录 |
| K26 | 256000 / 64000 | 同属 Kimi 不等于预算相同；BenchLM coding 58，只作替补 |
| K3 | 1000000 / 128000 | 免费入口里输出上限最高的一档 |
| GFX | 1000000 / 64000 | GF 的快速版（用户实测），速度≈DS V4.1 Flash 1017；仅 text，付费 x0.31 |
| C37 / C36 | 1000000 / 32768；1000000 / 65536 | 不沿用其他通道或原生型号上限 |
| G / GF 系列 | 1000000 / 128000 | GFX 只给 64000 |
| S1 / S2 | 1000000 / 64000 | 两条免费入口容量相同 |
| H42 | 1000000 / 64000 | 未声明输入列表，按文本 |
| H31 / H32 | 192000 / 64000 | hy3 上下文只有 192K，长文任务先分块 |
| MS | 1000000 / 96000 | 免费 contributor，输出上限仅次于 D 系；未声明输入列表，按文本 |

## 四、effort 声明与真实语义

| 入口 | 配置标签与历史证据 | 指派建议 |
|---|---|---|
| D1 / D2 / D3 / DB / DC | 配置均 off / low / high / max；历史 D1 菜单只有 high，D2 为 disabled / low / high / max；D3、DB 菜单本次未核实 | 不认定四档原生可控，未核实省略 |
| DC | 历史 5 次最小调用：off 无 reasoning_content；low / high / max 约 30～36 reasoning tokens，无梯度；high 延迟曾见 53s 与 1.45s | 历史 off 被观察到关闭，不承诺当前梯度或速度 |
| K31（= K3 的 buddy 渠道）/ K27 | 配置 low / high / max 与 high；历史 Buddy 菜单只有 medium，与配置不一致 | K27 已决定取消（2026-10-18），此行仅供对账 |
| K28 | 配置 off / high；此前菜单同名 | **已决定取消（2026-10-18）**，仅供对账 |
| K3 / K26 | 配置 low / high / max 与 off / high；K3 免费入口的历史菜单曾只有 medium | 以通道实际回显为准，不跨入口照搬 |
| QX / Q1 / Q2 | off / low / medium / xhigh，历史映射 none / low / medium / xhigh | 不套其他 Qwen 入口开关 |
| MM | off / high，历史映射 none / on | 开关而非强度，且无倍率标记 |
| H42 | off / high，历史菜单只有 high | off 关闭未核实 |
| G / GF / GF2 / GF3 / GFX / GX | low / high / max，无 off 档 | 不给「关闭思考」的承诺；GFX 与 GX 同标签不同通道；GFX 速度≈DS V4.1 Flash 1017（用户实测），仅 text |
| M1 / M2 | off / low / medium / high；历史 M2 三档按共享输出预算 4096 / 16384 / 32768 换算 | 不是原生强度；M1 是 Cline 菜单，M2 是共享预算，两者不可互换 |
| S1 / S2 | low / medium / high；旧 cn-f 时代同类入口按 2048 / 8192 / 32768 预算换算 | 不把这组数字当当前 step-f 的契约，截断先查预算 |
| MS | 配置 off / low / medium / high；本轮未做历史核实（新入口） | 不承诺「关闭思考」；先按默认跑，有回显再记 |

## 五、证据时效与维护

此前校准参考过本机 `/v1/models`、`/v1/reasoning-efforts`、适配器、请求序列化与部分最小调用；均为历史证据，本次未在线复验。旧「24 / 30 / 32 条白名单」「当前主模型」「Raccoon 付费替补」「cn-f / en-f 时代别名」不再当现行事实。

本次只解析 YAML，核对当前 30 条白名单（含新增 MS）、显示计费分类、输入、effort、容量与文档；没有改 profile / 凭据 / 插件，没有模型生成。配置变化重新同步 [路由入口](../skills/model-routing.md) 与 [委派协议](subagent-driven-dev/protocol.md)，不扩张会话政策或用户计费授权。
