# DSH 全局约定

> 用户级指令位：DSH `dsh-agent-instructions` 的 `user-global` → `$DSH_HOME/AGENTS.md`。
> 本机 `$DSH_HOME` = `C:\Users\19059\.dsh`（DSH 桌面通道）。
> 本文件不覆盖系统提示、开发者指令或用户的当轮明确要求。
> 禁止在用户目录做递归删除

## 委派、模型与团队协作（本机）

- **AgentTeams 是唯一的团队协作协议**：判定命中委派门槛 → 插件 `dsh-agent-teams-fish`，工具 `agent_teams_*`，面板在右侧栏「团队协作」。**看任务结构，不看用户是否说出「组队」**；只建最小 DAG，不凑人数。
- 单点、一次性、无需协作（读一个文件 / 跑一条命令 / 检索后总结）→ `subagent` / `subagent_fork`；任务紧密耦合、需完整会话上下文 → 主 agent 自己做，不委派。
- ⛔ **禁用**官方实验包的 `spawn_teammate` / `team_task_*`：两套协议并行会互相干扰、状态互不可见。
- **主 agent 自身路由不能自换**；只有用户可以在模型选择器里切换。能换模型的只有委派（AgentTeams 成员 / `subagent` / `workflow` 脚本里的 `agent()`）。
- 可用模型 = desktop profile 的 `subagent-model-selection-settings.allowedModels`；传白名单外的 `provider` / `model` 会被会话策略**直接拒绝**。
- **协议正文以 skill 为准**，本文件只留边界与指针：
  - 形态判定、captain 协议、任务 DAG、质量门契约、派活模板、两阶段审查 → `subagent-driven-dev`
  - 成员 / 子代理模型路由、预检、限流降级、成本纪律 → `model-routing`

### 本机硬约束（2026-10-20 核对）

- **派子代理 / AgentTeams 前必须先问用户用哪个通道**：官方 `deepseek-account/deepseek-flash`，还是 `tianxun` / `d1` 的某个模型。得到答复再派，不得自行默认；用户没有特别偏好时可建议官方优先。
- 本机 provider：`deepseek-account`、`tianxun`（天讯）、`d1`。`d1` 已配置但**不在委派白名单**。
- 白名单 = `deepseek-account/deepseek-flash` + `tianxun` 全部模型（清单见 `model-routing`）。白名单会随配置变化，路由前用 `list_subagent_models` 复核。
- `deepseek-official` 只是 web_search 的 provider，不是 LLM 路由。
- 成员默认 `memberMaxDepth: 0` —— **不要让成员再去开子代理**。
- 团队状态保存在**工作区**的 `.agent-teams/`（`team.json` + `inbox/*.jsonl`），属运行数据：**不要手工改写**；需要清理用 `agent_teams_delete` 归档。
