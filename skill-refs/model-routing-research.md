# 模型路由历史资料（旧榜单与套餐定价）

> **历史归档，不是当前可调用清单或选型依据。** 本文保留旧榜单、旧套餐、旧路由与当时的判断，仅供追溯；正文中的「白名单」「默认」「首选」「旗舰」「现仅」均指历史快照，不是当前会话事实。
> 当前入口与替补规则只维护在 `$DSH_HOME/skills/model-routing.md`，可用性以当前 profile 白名单为准。本次核对为 **24 条路由：22 条显示免费，2 条 Coding 套餐**；旧 bailian 四模型已不在外派白名单。
> 新增免费反代入口的上下文 / 输出限长、速度、幻觉率、实际费用与工具兼容性未逐路实测。下面的旧数字**不能直接迁移到同名免费入口**；新版本模型也不能借旧版本榜单排序。
> 本文件不是 skill，不自动加载；只有追溯旧定价 / 调研时按需读取，不用历史付费推荐覆盖当前的免费优先策略。
> **当前能力排序已换口径（2026-10-18）**：按桌面存档的 2026-10 能力表（`code_v3` 七题 + `logic`）排，见 [路由参考](model-routing-guide.md) 第二节；本文第二节的旧 benchmark 锚点排序仅作历史对照，不再作为选型依据。
> 历史事件：2026-10-07 移除了旧百炼 Max 路由，当时白名单为 6 条；当时用百炼 DeepSeek 做终审的决定现已失效。
> 历史事件：2026-10-05 删除 atria 套餐，墨点条目随之失效。GLM 当前已有免费入口，不能沿用「仅百炼可用」的旧结论。Coding Qwen 的 effort 仍以当前工具返回为准。

---
## 二、能力排序（benchmark 锚点，口径不可跨榜直比）

**综合（BenchLM 百分制聚合，2026-09）：**
`qwen3.8-max` 71.69（#14）＞ `glm-5.3` 65.44（#29）＞ `qwen3.8-flash` 60.82（#45）＞ `qwen3.7-plus` 55.83（#54）≈ `deepseek-v4.1-flash` 55.7（#55）＞ `minimax-m3` 54.78（#63）≈ `qwen3.6-plus` 54.57（#65）＞ `kimi-k2.7-code` 50.45（#82）

**分维度要点：**

| 维度 | 排序与锚点 |
|---|---|
| 编程（SWE/终端） | `qwen3.8-max`（SWE-Pro 67.7、TB2.1 86.6）＞ `glm-5.3`（AA Coding 74.8、TB2.1 88.2）＞ `deepseek-v4.1-flash`（TB2.1 90.6）＞ `kimi-k2.7-code`（SWE Vals 78.2 开源第一）＞ `qwen3.8-flash`（SWE-Pro 62.5）＞ `qwen3.7-plus` ≈ `qwen3.6-plus`（Verified 77.7/78.8，Pro 仅 ~57） |
| Agent / 工具调用 | `qwen3.8-max`（OSWorld 86.1）＞ `glm-5.3` ＞ `qwen3.7-plus`（MCP Atlas 73.2、τ² 93）＞ `deepseek-v4.1-flash` ＞ `qwen3.8-flash`（长链路会卡死）＞ `minimax-m3` |
| 长上下文 | `deepseek-v4.1-flash`（AA-LCR 84.0、1M+384K 输出）＞ `qwen3.8-max`（MRCRv2 92.9）＞ `qwen3.7-plus`（MRCRv2 91.7）；`minimax-m3` 宣称 1M 实际保证 512K |
| 多模态 | `qwen3.8-max`（MMMU-Pro 82.3）＞ `qwen3.8-flash`（MathVision 90.6）＞ `qwen3.7-plus` ＞ `deepseek-v4.1-flash`；atria 两路由 `glm-5.3`、`minimax-m3` **均仅文本** |
| 速度（输出 t/s） | `deepseek-v4.1-flash` ~217 ＞ `qwen3.7-plus` 147.5（agent 端到端）＞ `minimax-m3` 125 ＞ `glm-5.3` 88 ＞ `qwen3.8-flash` ~70（长任务实测极慢）＞ `qwen3.6-plus` 41.5；`qwen3.8-max` 官方 API 速度未查到（自托管单路仅 8–9） |
| 可靠性（1−AA-Omniscience 幻觉率） | `qwen3.7-plus` 27.7% ＞ `qwen3.6-plus` 34.6% ＞ `qwen3.8-flash` 45.3% ＞ `kimi-k2.7-code` 82.4% ＞ `deepseek-v4.1-flash` 96.5%（事实性最差，高事实性任务必须加校验）；glm/m3 未查到 |

**结论：** 榜单综合天花板本是 `qwen3.8-max`（**已于 2026-10-07 彻底移除，只能用它的名次做能力参照**）；本机主 agent 默认路由是 `bailian/deepseek-v4.1-flash`。白名单内综合与编程/agent 首位是 `glm-5.3`（仅文本、Credits 计费，只做硬任务）；**旗舰判断位按用户决定交给 `bailian/deepseek-v4.1-flash`**（1M + 384K 输出 + 最快，适合上下文卸载型终审）；「又强又稳又快」的打工位是 `qwen3.7-plus`；`kimi-k2.7-code` 综合分低但纯代码赛道开源第一，属偏科尖子。

---

## 三、套餐定价与性价比排序

**三套计量体系，不可直接比单价，只比「每元订阅费买到的有效产出」：**

| 体系 | 路由 | 计量与额度 | 超限行为 |
|---|---|---|---|
| Coding 套餐 | `bailian-he` 两条 | ¥200/月＝90,000 次调用（0.0022 元/次）；5h 额度滚动恢复 | 官方未明写转按量；套餐 Key 与按量 Key 不互通 |
| Token Plan | `bailian` 四条 | Credits 月额度（个人 Lite 39 元/11,500 → Pro 499 元/180,000）；**Credits ≈ 按量原价元 × 100**（单样本反推，已用官方示例校验） | 触顶**暂停服务**，不自动转按量、不结转 |
| 墨点 | `atria` 两条 | 10 墨点/5h、50/7d；RPM 50、TPM 2M；墨点/百万 tokens：`glm-5.3`＝2/8/28、`minimax-m3`＝0.42/2.1/8.4（缓存/入/出）；1 墨点≈最多 50M 缓存 tokens（以 DeepSeek-V4-Flash 为例） | 墨点不足**拒绝新调用**（不透支、不转按量）；鉴权/参数/平台错误的失败请求不扣；上游分不清缓存时按未命中计；墨点按有效期清空（按月发放＝自然月周期） |

**Credits 折算系数（每 1K tokens，入/缓存/出；推算值，以控制台用量详情为准）：**
`qwen3.8-flash` 0.08/–/0.27 ｜ `deepseek-v4.1-flash` 0.1–0.2/0.01–0.02/0.4–0.8 ｜ `kimi-k2.7-code` 0.65/0.13/2.7 ｜ `qwen3.8-max` 1.2/~0.15/3.6

**墨点折算系数（每 1K tokens，缓存/入/出，官方正式价）：** `glm-5.3` 0.002/0.008/0.028 ｜ `minimax-m3` 0.00042/0.0021/0.0084。
额度体感：10 墨点/5h ≈ `glm-5.3` 未命中输入 1.25M 或输出 357K；≈ `minimax-m3` 输入 4.76M 或输出 1.19M。注意墨点与 Credits 是**两种货币**，只能各自池内比、跨池比「能力/配额稀缺度」。

**夜间折扣（22:00–08:00）：** `qwen3.8-flash` 4 折；`deepseek-v4.1-flash` 5 折（`qwen3.8-max` 原有 4 折随其下线失效）。可夜跑的批量任务尽量排夜间。

**性价比推荐序（= 第一节行序）与理由：**

1. `qwen3.7-plus`：固定月费摊到单次调用趋零，能力/速度/幻觉率均衡 → 一切默认外派位。
2. `qwen3.8-flash`：Credits 单价最低档，夜间再 4 折 → 高并发轻量与图文。
3. `deepseek-v4.1-flash`：闲时半价 + 夜间 5 折 + 缓存 10%，长文本命中缓存后几乎白嫖 → 长文本/多模态。
4. `qwen3.6-plus`：与 3.7 同额度但更慢更弱，同套餐内主动用等于亏 → 只作降级替补。
5. `glm-5.3`：Credits 计费，能力居白名单综合 / 编程第一梯队 → 硬任务的高价值出口（**墨点池随 atria 删除已不存在**，本条已按当前计费更正）。
6. `kimi-k2.7-code`：Credits 为 flash 的 8–10 倍，只在「复杂纯代码一次做对」时回本。
7. `minimax-m3`：墨点池内的长文本兜底；单价仅≈`glm-5.3` 的 1/4，轻任务先用它、把 glm 配额留给硬任务；能力平平不主力。
8. ~~`qwen3.8-max`：最贵一档，只买「旗舰判断」——仲裁、终版二审、长程自主~~（**2026-10-07 已彻底移除，本条作废**）。现在的旗舰判断位是 `bailian/deepseek-v4.1-flash`（按用户决定；长上下文 + 最快 + 夜间 5 折，但幻觉率最高，事实性仲裁要附来源），长程自主活建议主 agent 自己做或拆成多个 deepseek 短任务。

---

## 附录 A：benchmark 与口碑来源（2026-09-29 检索）

- 榜单聚合：benchlm.ai/models/{qwen3-8-max, qwen3-8-flash-next, qwen3-7-plus, qwen3-6-plus, kimi-k2-7-code}、benchlm.ai/compare/qwen3-6-plus-vs-qwen3-7-plus
- Artificial Analysis：artificialanalysis.ai/models/minimax-m3、commandcode.ai/models/qwen3-8-max-0902（引 AA v4.3）
- 口碑实测：lcz.me/topic/1349（3.8-Flash 长链路卡死）、juejin.cn/post/7669743766019457076（3.8-Max 工程严谨度）、atlascloud.ai（3.7-Plus BugFind 10/10）、mindstudio.ai/blog/glm-5-3-benchmark-test-results、pub.towardsai.net（DeepSeek V4.1 Flash 实测）、reddit r/LocalLLaMA 与 r/singularity 相关帖
- 四模型专项：z.ai/blog/glm-5.3、deepseek.com/zh/news/deepseek-v4-1-flash、kimi.com/resources/kimi-k2.7-code、302.ai 实测（deepseek-v4.1-flash / minimax-m3）、infoq.cn（GLM-5.3 测试范围警示）、github.com/anomalyco/models.dev/issues/2070（M3 的 1M 仅保证 512K）
- **口径警告：** BenchLM 百分制、AA Intelligence Index、AA v4.3 引值三者不可横向直比；表内名次仅同榜内有效。
- **冲突项：** 3.8-Max AA 指数 45（0902）vs 社媒 56 未证实；3.6-Plus AA 估算 27 高于 3.7-Plus 25.2 与 BenchLM 排序相反；3.8-Max 官方 API t/s 未查到。

## 附录 B：定价来源

- 官方：help.aliyun.com/zh/model-studio/{token-plan-overview, token-plan-personal-overview, model-pricing, coding-plan, kimi-k2-7-code, glm-5-3, deepseek-v4-1-flash, context-cache}
- 推算规则：Credits = 按量原价元 × 100（仅 qwen3.6-plus 单样本校验通过，置信度中）
- atria 墨点：官方文档 `04-model-limits-and-pricing` 登录态全文（2026-09-29 用户提供，存档 `atria-modian-pricing.md`）+ 本机配额页截图

---

