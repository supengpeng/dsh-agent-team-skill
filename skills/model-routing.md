---
name: model-routing
description: 【P0-2｜命中委派门槛时加载】本机模型路由——派活前先问用户选官方还是其它通道，再做选型、预检、预算与故障兜底。与 subagent-driven-dev 配对使用。
---

# 模型路由（本机版）

> 2026-10-20 按本机 desktop profile 重写。废弃来源机器的反代白名单（`deepseek-f` / `glm-f` / `kimi-f` / `god` 等，`127.0.0.1:18900`）；本机没有这些入口，不要派。

## 〇、派活前先问（硬规则）

派任何 `subagent` / `subagent_fork` / AgentTeams 成员前，**先问用户**：用官方 `deepseek-account/deepseek-flash`，还是 `tianxun` / `d1` 的具体模型？得到答复再派，**不得自行决定**。官方优先只是建议，不是默认授权。

## 一、本机可用入口

白名单（`subagent-model-selection-settings.allowedModels`，编辑于 `C:\Users\19059\.dsh\profiles\desktop\cordis.patch.yml`）：

- `deepseek-account/deepseek-flash`（DeepSeek V4.1 Flash，账号通道）
- `tianxun/<模型>`：`4.1ftest`、`MiniMax-M3.1-Flash`、`deepseek-v4-flash-0731`、`deepseek-v4-pro-0813`、`deepseek-v4.1-flash`、`deepseek-v4.1-flash-cache`、`deepseek-v4.1-flash-fastest`、`gemini-3.1-flash-image`、`gemini-3.8-flash-high`、`glm-5.3`、`glm-5.3-flash`、`glm-5.3-flash-512k`、`glm-5.3f-test`、`gpt-6-astra`、`gpt-6-luna`、`gpt-6-sol`、`gpt-6.1-sol`、`grok-4.7`、`hy-4`、`kimi-k3`、`mimo-v2.6-flash`、`qwen3.8-flash`、`qwen3.8-flash-512k`、`qwen3.8-max-0902`、`qwen3.8-omni-flash-256k`

`d1/gpt-6-astra` 已配置但**不在白名单**；其余未列出的 provider 同理。白名单是配置声明，不代表在线、有额度或本会话已生效。

## 二、按任务选型

用户答复用官方时，首选 `deepseek-account/deepseek-flash`；答复用 `tianxun` 时按表内 tianxun 顺序。`tianxun` 各模型计费/额度未核实，派前再次确认。

| 任务 | 首选 → 备选 |
|---|---|
| 默认 / 通用 | `deepseek-account/deepseek-flash` → `tianxun/deepseek-v4-pro-0813` → `tianxun/kimi-k3` |
| 单文件实现 / 小改动 | `deepseek-account/deepseek-flash` → `tianxun/kimi-k3` → `tianxun/glm-5.3` |
| 前端 / UI | `deepseek-account/deepseek-flash` → `tianxun/gpt-6-astra` → `tianxun/deepseek-v4.1-flash` |
| 真实项目编码 / 多步 Agentic | `deepseek-account/deepseek-flash` → `tianxun/deepseek-v4-pro-0813` → `tianxun/kimi-k3` |
| 数据处理 / 抽取 | `deepseek-account/deepseek-flash` → `tianxun/qwen3.8-max-0902` → `tianxun/kimi-k3` |
| 逻辑 / 数学 / 推理 | `deepseek-account/deepseek-flash` → `tianxun/deepseek-v4.1-flash` → `tianxun/kimi-k3` |
| 截图 / 视觉 | 官方声明支持图像时用 `deepseek-account/deepseek-flash`；否则 `tianxun/gemini-3.8-flash-high` → `tianxun/qwen3.8-omni-flash-256k` |
| 低成本批量摘要 | `deepseek-account/deepseek-flash` → `tianxun/glm-5.3-flash` |
| 独立复审 | 实现用官方 → 复审用 `tianxun/kimi-k3` / `tianxun/glm-5.3`；实现用 tianxun → 复审用官方 |

> 排序参考来源机器对同一批模型家族的榜分（2026-10），**不是本机实测**；`gemini` / `grok` / `gpt-6` 的计费与限额也未逐一核实。官方通道之外的入口先用小任务验证。

## 三、指派前预检

1. 写清输入模态、工具需求、上下文/输出规模、风险、数据敏感度与计费授权；先过滤能力，再排序费用。
2. 用 `list_subagent_models` 核对目标 `provider` / `model`：目录未列出 = 白名单外，不要传；目录列出 ≠ 在线/有额度/本会话生效。
3. 显式路由成对传 `provider` / `model`，保留完整 ID；省略是继承，不代表免费。主 agent 不自换路由。
4. effort 按目标声明核对；未核实就省略，让默认生效。
5. 图像必须有 `image` 声明与真实传图链路；敏感输入先确认端点隐私政策。

## 四、失败与预算

- 顺序：免费/已包含优先 → 同模型其他入口 → 兼容模型 → 经用户批准的付费兜底。
- 每任务每轮最多 3 个不同完整路由（含首选），每入口最多一次；重派、换链、自动续轮不清零。
- 策略拒绝、鉴权、参数/effort 错误先查原因，不遍历入口；限额按入口/账号冷却；质量失败查根因，不冒充限额。
- 最终失败后核对旧执行、关联作业与副作用，再按 `subagent-driven-dev` 安全重派。
