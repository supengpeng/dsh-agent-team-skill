# 视觉伴侣（visual companion）操作手册

> 按需读：P1 `brainstorming` 的「视觉化呈现」只在**真要给用户看图**时才需要本文件 —— 不看图就一行 token 都不用花。
> 插件：`@fish-under-sea/dsh-visual-companion`（本机 v0.1.5，宿主插件 + 零依赖本地服务，host-only）。

## 一、首选：在会话里输入 `/companion [主题]`

插件注册的斜杠命令会一次做完三件事：**①** 本地服务没在跑就后台拉起（零依赖、自动分配端口）；**②** 把带会话密钥的完整 URL 交给当前会话的助手，由它用 `sidebar_open` 开在右侧栏；**③** 让助手把第一屏原型写进 `screen/`。

`watchDir` 是实际配置的路径，不保证等于当前工作区。本机曾配置到另一个默认工作区；不能据此承诺当前会话加载即绑定或重启后仍可用。

1. 以 `/companion` 返回的实际 `screenDir` / `stateDir` 确定服务根目录，不根据当前工作目录猜测。
2. 用 `visual_companion({action:"status"})` 检查绑定目录和目标会话。
3. 未绑定、目录不同或会话不符时，用 `visual_companion({action:"arm",dir:"<实际根目录>",session_id:"<当前会话>"})` 绑定，再查 `status`。不能核实时告知用户，不声称已经建立唤醒链路。
4. `wake` 会真实投递消息并启动一轮，不为本文件检查或静态验证自动调用；需实际验证时先说明影响。

## 二、工具面 `visual_companion`

| action | 参数 | 作用 |
|---|---|---|
| `arm` | `dir`、`session_id?` | 绑定目录与目标会话（`session_id` 可省略，之后由 `pending.json` 自带） |
| `disarm` | — | 解绑并关闭观察器 |
| `status` | — | 绑定状态、唤醒次数、最近一次结果、当前 `pending.json` |
| `wake` | `session_id`、`text` | 立刻探测唤醒链路（不经过页面，验证用） |

## 三、手动控制（需要自己接管时）

1. 起服务（受管后台任务）：

   ```powershell
   node "$env:DSH_HOME\profiles\<profile>\node_modules\@fish-under-sea\dsh-visual-companion\bin\visual-companion.mjs" `
     --dir "<工作区>\.dsh-visual" --port 0 --session "$env:DSH_SESSION_ID"
   # → 打印 { url, port, key, screenDir, stateDir, pendingFile, session }
   ```

2. 把 HTML 片段写进 `<dir>\screen\`（语义化文件名、别复用；要完全控制页面时才写完整文档，即以 `<!DOCTYPE` / `<html` 开头）。
3. 用 `sidebar_open` 打开 **`url` + `?key=`** —— **缺 key 会被 403 拒绝**。

服务自带的框架样式，写片段即可用：选项 `.options > .option[data-choice]`（容器加 `data-multiselect` 变多选）、视觉块 `.mockup` `.split` `.pros-cons` `.mock-nav` `.mock-sidebar` `.mock-content` `.mock-button` `.mock-input` `.placeholder`、文字层级 `h2` / `h3` / `.subtitle` / `.section` / `.label`。

落盘都在 `<dir>\state\`：`pending.json`（**只有提交才写**，插件观察它）、`events.jsonl`（`click` 不唤醒 / `submit` 唤醒一次）、`server-info` / `server-stopped`。

## 四、唤醒语义（为什么它省 token）

- **点选过程完全静默**：任意点选、取消、填备注都不发请求。
- **只有按「提交给助手」才唤醒一次**，整组选择 + 备注拼成一条用户消息，例如
  `【视觉伴侣】用户在页面上选好了 2 项：A（单栏布局）、C（混合方案），备注：间距再大一点。已同步给你，直接往下走就行（不必再问他）。`
- 会话收到后自动起一轮接着干，用户不必回终端复述。

## 五、降级链（缺哪环退哪环）

| 缺什么 | 退到 |
|---|---|
| 没有 Node | 写自包含 HTML + `present` |
| 没有侧边栏浏览器 | 把完整 URL 交给用户手动打开 |
| 插件都没装 | 下一轮手工读 `events.jsonl` |
| 全都不行 | Markdown 表格 / ASCII / Mermaid —— **不要卡住流程** |

## 六、成本与边界

- **成本纪律**：伴侣的文档与脚本都随插件包发布，**不进默认上下文** —— 不看图就不读包内 README、不起服务。
- 服务**闲置 4 小时自动退出**（`--idle-minutes` 可调）；**原型文件是临时产物，别当交付物**。
- 需要 Node `^22.19.0 || >=24`、DSH `>=0.2.0-rc.2`，不需要任何 API key。
- 远程 / 容器场景用 `--host 0.0.0.0 --url-host <主机名>`，但**必须保留 `?key=`**。
- 第一次需要时**单独发一条消息**只问这一件事，并说明会多花 token；对方拒绝就纯文本继续，别再提。