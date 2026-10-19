# BenchLM Data 查询方法（第二证据源，默认不启用）

> **默认不启用。** 只有「更新路由技能 / 刷新白名单与能力表 / 回答模型定位问题」时才按本文件取数；日常任务路由、日常委派选型**不查**它，也不为排序空跑。
> 密钥存于 DSH 凭据库 `C:\Users\fish\.dsh\.credentials.yaml` 的 `refs` 段，键名 `BENCHLM_DATA_KEY`（与 `GOD_API_KEY` 同一位置）。**不在任何文档、脚本、回复里写明文**；读法见第二节。

## 一、定位与边界

- 它是聚合榜（141 个 Arena 榜 + Epoch AI + 厂商模型卡表格），**不是单一题库**；只能当家族级「整体位置」旁证。
- 有 `code_v3` 行的家族，`code_v3` 优先；BenchLM 只做交叉验证。没有 `code_v3` 行的家族（S、H42、M1/M2、K26、C36/C37、MS）用它补位；GFX 两处都无分（只用用户实测定位），K27 / K28 已决定取消。
- 口径：BenchAlign v5.8（2026-09-30），`dataAsOf` 随响应返回（本轮为 2026-10-16）。引用必须带源 + 日期。
- 白名单 ID ↔ slug 对照表在 [第三方月榜](model-benchmark-ranking.md) 第七节，不在这里重复。

## 二、凭据与通道

- 凭据：`C:\Users\fish\.dsh\.credentials.yaml` → `refs.BENCHLM_DATA_KEY`（Free 档，250 次成功读取/月、30 请求/分；本轮核对时 `successfulReads` 30 / 250）。
- **本机直连不通**：`Invoke-WebRequest` / `curl` 走 schannel 报 `The SSL connection could not be established` / `SEC_E_NO_CREDENTIALS`。
- `web_fetch` **带不了** `Authorization` 头（匿名请求只会拿到 marketing 页）。
- **唯一可行通道 = Node 24 的 `fetch`**（自带 OpenSSL，不走 schannel）。本机 node 24.14.0。
- 读密钥（PowerShell，不打印明文）：

```powershell
$cred = 'C:\Users\fish\.dsh\.credentials.yaml'
$env:BENCHLM_DATA_KEY = [regex]::Match((Get-Content $cred -Raw -Encoding utf8), '(?m)^  BENCHLM_DATA_KEY:\s*(\S+)\s*$').Groups[1].Value
if (-not $env:BENCHLM_DATA_KEY) { throw 'BENCHLM_DATA_KEY not found in credentials refs' }
```

## 三、查询配方

```powershell
node --input-type=module -e "const H={headers:{Authorization:'Bearer '+process.env.BENCHLM_DATA_KEY}};
const j=await (await fetch('https://data.benchlm.ai/v1/rankings/current?surface=coding&limit=100&offset=0',H)).json();
console.log(JSON.stringify(j.items.map(x=>({r:x.rank,s:x.slug,v:x.score,m:x.maturity,f:x.evidenceStatus})),null,1));"
```

- 榜单列表：`GET /v1/rankings/current?surface=overall|coding|agentic|knowledge&limit=100&offset=N`（`limit` 上限 100；单 surface 最多 220 行，分页取）。
- 单模型四榜一次给全：`GET /v1/models/{slug}/rankings/current`（返回键是 `rankings`，不是 `items`；缺榜 → 404 `{"error":{"code":"current_ranking_not_found"}}`）。
- 模型目录：`GET /v1/models?limit=100&offset=N`（约 954 条，**`search` 参数不支持**，只能分页后自己筛）。
- 余量：`GET /v1/usage`（`successfulReads` / 月限；不扣额度）。

## 四、错误与限制

| 现象 | 含义 |
|---|---|
| 400 `invalid_query` + `Use the documented query arguments.` | 参数不在白名单（如 `limit=200`、`search=`） |
| 404 `current_ranking_not_found` | 该 slug 无榜分（如 `muse-spark-1-3`、`muse-wonder-1-0`） |
| 401 / 403 | 密钥错或未带 `Bearer` |
| `rankings/current` 里找不到某模型 | 它可能不在分页范围，改走单模型端点（K3 就是这样） |

## 五、行字段（写文档时只取需要的）

`rank`、`benchAlignRank`、`supportedRank`、`modelKey` / `slug`、`name`、`creator`、`score`、`evidenceStatus`（supported / estimated）、`maturity`（established / developing / preliminary / benchmark-only）、`scoreInterval90Lower/Upper`、`intervalScope`、`intervalValidated`、`sourceFamilyCount`、`evidenceClassCount`、`lineageNote`、`capabilityGroupKey`、`evidenceRoute`。

## 六、写进文档的规范

- 格式：「BenchLM `<dataAsOf>`（BenchAlign v5.8）：`<slug>` overall `<名次>`/`<分数>`、coding `<名次>`/`<分数>`、agentic `<名次>`/`<分数>`，置信 `<maturity>`/`<evidenceStatus>`」。
- `developing` / `preliminary` / `benchmark-only` / `estimated` 必须显式标注为低置信，不能当硬结论。
- 与 `code_v3` / `logic` 冲突时，任务级近期数据优先，并在结论里写明两源各自的日期。
- 不把 `score` 当百分比，不跨 surface 比较绝对分；排相对位置用名次。

## 七、禁止事项

- 不把本查询放进日常路由、预检或成员 prompt；不因「想确认能力」就空跑。
- 不把密钥写进 skill、文档、git、临时脚本；不在回复里贴完整 key。
- 不为凑数批量翻页（250 次/月是硬上限，一次 `limit=100` 算 1 次）。
- 不把 BenchLM 名次当成本机验收结论——它替代不了真实任务跑分。
