# 协作与安全重派按需协议

> 属于 `subagent-driven-dev` 的详情层，不自动加载。建团队读一；写任务读二；审查读三；并行读四；重派前读五。只读触发的小节；路由、effort、冷却与次数预算只维护在 [路由参考](../model-routing-guide.md)。

## 一、AgentTeams 主路径

### 1.1 工具面

`create`、`approve`、`edit_plan`、`add_member`、`remove_member`、`create_task`、`reassign_task`、`claim_task`、`update_task`、`amend_task`、`send_message`、`status`、`resume`、`delete`。

本机插件为 `dsh-agent-teams-fish`，其他机器可能是上游包。工具名均为 `agent_teams_*`，按实际工具判断；面板在 DSH Web 右侧栏「团队协作」。

### 1.2 captain 协议

1. 按需先看 `agent_teams_status`。已有团队就继续，**不要重复花名册、任务或派活**。
2. 没有团队才用 `agent_teams_create`，默认 `approval="required"`。普通研究 / 审计可一次传 `plan: { members, tasks }`；**原子 plan 的任务是普通 work**，质量类任务用 `create_task` 显式创建契约，整个最小 DAG 在暂存阶段建完。
3. 每个任务必须有 `subject`；需要显式 kind / owner 时给 `kind` / `assignee`，不能只写在标题或成员角色里。每个必需贡献者有任务或明确消息。
4. `dependencies` 是前置任务 ID，全部前置完成才可 claim。新任务由调度器派发，不用消息催启动。
5. 暂存后停下，等 Web **Approve & Run** 或后续用户明确批准。**创建 / 编辑计划的同一回合不得自我批准**；仅用户明确要求立即运行时可用 automatic。
6. 派活、重试、换人用 `reassign_task`，先吊销旧 attempt 再交新 owner。captain 不替成员 claim；成员每次更新必须带当前 `attempt_id`。
7. 不忙轮询；收到汇报或用户问状态时才查 status。等待成员时结束本轮，汇报会唤醒 captain。
8. 运行中 `edit_plan` 只改从未启动的 pending 任务；**不能改活跃成员路由或已开始任务的契约来实现替补**。换入口走第五节。
9. 用户停止后团队 halted，后续用户明确要求继续才调用 `resume`。不得为额度替补自行恢复 halted 团队，也不得把限额当成接管实现 / 审查的用户授权。
10. 全部必需任务终态、成员 idle / ready 后汇报，再 `delete` 归档。未完成工作未经授权不丢弃；部署另需用户确认。

### 1.3 质量门契约

| kind | 必填 |
|---|---|
| `requirements` / `review` | objective + acceptance；review 还要 reviewedTaskId |
| `implementation` / `repair` | objective + acceptance + inScope + verify |
| `verification` / `integration` | objective + acceptance |

- requirements / review 完成必须 `verdict=pass`；`needs_revision` / `reject` 必须失败并带 findings（id、severity、problem、requiredFix）。不能自己批准自己的实现，也不能要求成员故意失败。
- 契约写错时用 `agent_teams_amend_task`：captain 专属、仅非终态、通过审查后冻结，修改记入 revisions。不能以入口限额为理由降低验收或伪造验证。
- 全质量模式在暂存时建完 `requirements → implementation → verification → review → integration`，integration 先依赖 review 第一轮。
- 失败审查由运行时自动补 repair / 下一轮 review 并改接下游，captain 不重复造循环、不依赖失败任务。

### 1.4 本机约束

- 只选已启用且会话白名单允许的 provider；`deepseek-official` 已停用，不能路由过去。路由目录与实际配额规则见 `model-routing`。
- 成员默认 `memberMaxDepth: 0`，**不得让成员自己开子代理或换模型**；成员上报失败，captain 执行替补。
- 工作区 `.agent-teams/` 是运行数据，不手改；清理用 `agent_teams_delete` 归档。

### 1.5 指派前预检与启动故障

- captain 私下记录目标完整路由、配置与本会话授权、输入模态、工具需求、上下文 / 输出余量、敏感资料边界、计费授权、effort 依据与本轮已试入口。费用顺序和共享三路由预算只维护在路由参考，不另建冲突链。
- 目录列出不是授权或在线证明；profile 刚更新但本会话仍拒绝时记录政策尚未生效，选已获准兼容候选或等待刷新会话，不改 ID 绕过限制。
- 建队暂存时确认工具面齐备；批准后若在模型启动前出现 `guardedContinuable` 或 `TypeError: Cannot read properties of undefined (reading 'call')`，先作为团队运行时故障收集错误与任务状态，不能当模型能力差或额度耗尽，也不为诊断空跑多个入口。
- 运行时故障本身不授权改插件或删除团队。保留 task ID、DAG、验收和既有产物；需要接管时先取得用户明确授权，再用 `reassign_task(assignee="captain")` 逐个接管就绪任务，每次本轮完成一个后再接下一个，不把 captain-owned 未完成任务留到空闲。
- 未获接管授权时如实报告宿主恢复条件与未完成任务；用户停止仍按 halted 规则，不借故障自行 resume。模型已启动后的限额 / 超时按路由参考分类，再按第五节安全重派。

## 二、派活契约

### 2.1 团队任务

- `subject`：一句可验收的目标。
- `description`：任务、上下文、依赖关系、交付物与非目标。
- `acceptance`：可判定的验收标准。
- `inScope` / `outOfScope`：工作区相对 POSIX 路径。
- `verify`：实际可运行的命令；implementation / repair 必填，从当前项目推导，不假定 `src/` 或 `pnpm test`。
- `kind` / `assignee`：显式声明。
- `deliverables` / 汇报约定：实际改动、执行命令与结果、文件定位、剩余风险；路由切换时附原入口、原因与目标入口。

### 2.2 单点任务

自包含模板与失败交接模板在 [自包含与失败交接模板](templates.md)。主 agent 负责选路由并给好边界，不把整份模型清单塞进子代理 prompt，不让子代理自己轮询免费入口。

## 三、两阶段审查

交付后按顺序执行：

1. **规格合规审查**：要求全部实现，没有多余功能或理解偏差。
2. **代码质量审查**：阶段一通过后再查架构、测试与生产就绪度。

团队路径建 `kind: review` 契约，两阶段写入 acceptance，以 `verdict=pass` 过门；单点路径由主 agent 复核或派独立审查子代理。**读代码验证，不信任自述报告**。

实现者不能批准自己的实现；同模型入口替补只补配额，不等于独立模型复审。关键任务优先选不同适配模型的独立审查者，具体选型见 `model-routing`。

## 四、并行分派

完全独立的问题域可以并行，但不按免费入口数推断并发容量。

- 团队：独立任务无依赖，由调度器并发；全部交付后检查冲突、审查并运行适合影响范围的测试。
- 单点：同一条消息发出，明确各自范围、目标、约束、输出；禁止同时改同一文件。
- 所有免费 provider 共用本地反代，整体故障时不跨 provider 忙试；额度范围、三路由预算及冷却见 [路由参考](../model-routing-guide.md) 第三节。

## 五、安全重派

次数预算和故障判断见 [路由参考](../model-routing-guide.md) 第三节；此处只处理生命周期与所有权。

### 5.1 AgentTeams：保留任务，换 owner

- **暂存时**：可用 `edit_plan` 的 `update_member` 更新完整路由；摘要展示改动后再次等待用户批准，不能同轮 approve。
- **运行时**：`edit_plan` 不能 update_member；`reassign_task` 也没有 provider / model 参数。先确认是否已有兼容的替补成员；没有则 `add_member` 建唯一名称的替补成员，保留 role、executionPrompt 与写入边界，并指定新 provider / model / 合法 effort。
- 对前置已完成、可重派的未完成 / failed 任务，用 `reassign_task({ task_id, assignee: "替补成员名", reason: "原入口限额，切换至目标完整路由" })`。它先吊销旧 attempt 并等待旧执行停下，再交新 owner；**不新建重复任务、不丢 DAG、不改验收条件**。
- 对从未开始且仍受依赖阻塞的 pending 任务，用运行中允许的 `edit_plan.update_task` 改 assignee，保留依赖 / 描述 / 任务 ID，不强行 claim。已完成任务不重跑；不是限额造成的质量门失败按原 repair 流程处理。
- 只有终态失败也确认旧执行停止时才重派；主动暂停活跃成员须用户明确要求。并发改写风险存在时先用 reassign 的吊销与静止机制，不能只发一句「停下」就启动替补。
- 新 owner 使用新 attempt，读取已有成果与故障证据后继续。captain 不替成员补一个假的 completed，也不因额度不足直接接管实现 / 审查。

### 5.2 subagent：结束旧执行，交接已有工作

- 当前调用最终失败 / 子代理已 inactive 才用新 `subagent` 重派；旧 agent 仍工作且需停止时先按授权 `interrupt_agent` 并等待实际停止，不让两个执行者同时写。
- 失败时只收到终态事件不一定意味着任务未产生副作用：先读已有改动，核对测试、提交、外部请求与后台作业。旧子代理有相关未结束作业时，由原执行者按权限收集 / 停止并确认静止，不能只看 agent inactive 就假定安全。
- 重派 prompt 保留原任务与验收，附已有结果、失败证据、允许继续 / 不得重复的操作；全新路由用 provider / model 指定，effort 重新核对。
- 不把新入口返回成功当验收成功；仍按第三节两阶段复核。主 agent 最终汇报原路由、失败类型、实际替补、是否降级及验证结果。
