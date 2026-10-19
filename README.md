# dsh-agent-team-skill

DSH 的 agent team / 子代理委派技能包：模型路由 + 委派协议 + 全局约定。

## 内容

| 路径 | 说明 |
|---|---|
| `AGENTS.md` | 用户级全局约定（`$DSH_HOME/AGENTS.md`） |
| `skills/model-routing.md` | 模型路由：派活前先问通道、选型、预检、预算兜底 |
| `skills/subagent-driven-dev.md` | 轻量委派协议：形态门槛、批准与验收、任务所有权 |
| `skill-refs/` | 按需参考（来源机器资料，部分路径/凭据在本机不存在） |

## 安装

```bash
DSH_HOME="${DSH_HOME:-$HOME/.dsh}"
cp AGENTS.md "$DSH_HOME/AGENTS.md"
mkdir -p "$DSH_HOME/skills" "$DSH_HOME/skill-refs"
cp skills/*.md "$DSH_HOME/skills/"
cp -R skill-refs/. "$DSH_HOME/skill-refs/"
```

技能由 `dsh-skill-filesystem` 扫描 `$DSH_HOME/skills`，`AGENTS.md` 由 `dsh-agent-instructions` 作为 user-global 加载。

## 规则

- 派 subagent / AgentTeams 前必须先问用户用哪个模型通道（官方 / tianxun / d1）。
- 用户没有偏好时建议官方 `deepseek-account/deepseek-flash` 优先。
- 模型白名单以本机 `cordis.patch.yml` 的 `allowedModels` 为准，配完需重启 DSH。

## 注意

本仓库内容来自作者本机安装副本：`AGENTS.md` 与 `model-routing.md` 中的 `C:\Users\19059`、`tianxun` 模型清单属于作者环境，使用时替换成你自己的 `$DSH_HOME` 与 provider 配置。
