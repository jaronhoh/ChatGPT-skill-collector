> 历史参考档案，非执行 Skill 或部署授权。来源：ChatGPT Library《Task_Tracker_2026-11-05_Skill_复盘与改进规范(1).md》。以下保留完整原文；历史建议不代表当前规则或平台能力。

---

# 2026-11-05 Task Tracker 工作复盘与 Skill 改进规范

> **用途**：作为更新 `skill_PRD_GitHub_Sites_Update_2026-10-24.md`（Skill 名称：`prd-github-sites-update`）的依据。该文档不是新发布指令，也不授权再次部署。  
> **项目**：`jaronhoh/Task-Tracker`；原站点 `https://task-tracker-jaron-migration.savoryboar128.chatgpt.site/`；原 Sites Project ID `appgprj_6ad0f51ede5c8191bb6ab29c1b9b8979`。  
> **最终业务决定**：用户确认现有 Sites 已具备所需功能，**不需要再部署本次 GitHub 变更**。  
> **证据层级**：`GitHub verified`＝本轮实时 GitHub 操作或 CI 结果；`Work reported`＝用户转述 Work 对 Sites 的检查；`User confirmed`＝用户亲自确认功能；`Not verified`＝无充分证据。**不同层级不得混用。**

## 一、最终结论（本次事件最重要的教训）

**项目完成状态应首先由用户希望达成的实际结果决定，而非由 AI 预设的流程步骤决定。**

本次 GitHub 的代码变更和自动化测试已完成，但 Work 无法把 GitHub `main` 源码同步到原有 Site。助手反复把这个技术缺口当成强制部署障碍，发出了多份重复 Work 指令，还追加了不必要的发布门槛。后来用户明确确认：**现有 Sites 本身已经具备所需功能**。因此继续部署没有必要。

正确的收尾是：保留现有正常运行的 Site，停止无意义的源码同步和发布尝试，将 GitHub 项目状态记录为 **「用户确认 Sites 功能可用；额外部署不需要；GitHub 与当前 Sites 源码对应关系未核实」**。不能把用户确认当成「PR #20 已在 Sites 上发布」，也不能因源码映射未证实就否认用户的实际验收。

## 二、2026-11-05 工作时间线与真实结果

| 阶段 | 当日操作及证据 | 结果与限制 |
|---|---|---|
| 需求确认 | CR-024 采用 **Option A**：取消公开/第三方 API 产品，但保留 React→D1 内部 `/api/tasks`、`/api/customers`、`/api/log-time`、`/api/health`。 | 不得误解为删除全部 HTTP API。 |
| 开发实施 | 在 PR #20 增加 `lib/request-policy.mjs`，对明确跨站请求和非 JSON 写入作请求完整性限制，并加入安全测试。 | Origin/Fetch Metadata **不是 Owner 登录认证**；没有改变 D1 Schema。 |
| 测试与修正 | 初次 Chromium 回归失败源于测试服务器把 `127.0.0.1` 请求重构为 `localhost`；修正保留 Host 后，完整测试绿灯。 | 实际测试 18 React/model + 69 API/D1/security + 18 Chromium＝**105/105**。 |
| 真实匿名访问 | 对原 Sites 进行 10 路由 × 两种无登录凭证 GET 测试，含模拟同源标头。 | **20/20 HTTP 401**；仅证明这些匿名读取被拒绝，不证明已登录非 Owner、所有写入方法已受保护。 |
| PR 状态误判 | Work 使用传统 Commit Status 接口，返回空的 `statuses`，推断当前 Head 缺少 CI。 | 实际 GitHub Actions 对**精确 Head** `7b70b051a416a4c2c91b95f705aa098201c0e2ab` 已成功；不能以传统状态列表为空否认 Actions。 |
| GitHub 合并 | 将 PR #20 从 Draft 改为 Ready；确认 mergeable；完成合并。 | **Merge Commit** `bee56116976558f4c10c8590b8a128e7048b1853`；与测试过的 Head 比较无文件内容差异。**合并不等于 Sites 部署。** |
| Work 生产检查 | Work 报告原 Site 仍为 **Version 15**、Owner-only allowlist 仅 Owner、D1 binding 为 `DB`，并读取五张表。 | 来自用户转述的 Work 检查；不是本聊天独立登录 Sites。 |
| Work 部署阻碍 | Work 有查看原 Site/D1、发布**已保存**版本的工具，但无查看/修复原 Site 托管源码关联、同步 GitHub `main` 到托管源分支的操作。 | **未保存新版、未生产部署**。反复改 Prompt 无法制造不存在的工具能力。 |
| 源 SHA 判断 | Sites v15 报源 SHA `251d602ee3c01f561017aba66ab2101a6e745241`；该 SHA 无法在 GitHub `jaronhoh/Task-Tracker` 中找到。 | 只能得出**来源未确认**，不能断言 Site 绑定错误；Sites 可能使用独立源码历史。 |
| 用户最终验收 | 用户表示 **「Sites 已经有我要的功能」**，并同意更新 GitHub 而不是继续部署。 | **User confirmed**：额外发布不再必要；不得声称 PR #20 已部署。 |
| GitHub 收尾 | 更新 `docs/PROJECT_STATUS.md`、`docs/operations/RELEASE_AND_RECOVERY.md`，将 Issue #23 以 **Not Planned** 关闭。 | 是取消不必要的额外发布任务，**不是**证明该 Issue 中所有手机/部署验收均已执行。 |

**关键 GitHub 证据**：

- [PR #20 — 已合并](https://github.com/jaronhoh/Task-Tracker/pull/20)
- [精确 Head 的 105 项测试通过](https://github.com/jaronhoh/Task-Tracker/actions/runs/41991309148)
- [精确 Head 的 20 次匿名 GET 401](https://github.com/jaronhoh/Task-Tracker/actions/runs/41991308993)
- [项目状态](https://github.com/jaronhoh/Task-Tracker/blob/main/docs/PROJECT_STATUS.md)
- [发布及恢复手册](https://github.com/jaronhoh/Task-Tracker/blob/main/docs/operations/RELEASE_AND_RECOVERY.md)
- [Issue #23：额外部署取消](https://github.com/jaronhoh/Task-Tracker/issues/23)

**Work 当日读取的 D1 基线**（仅供追踪，非完整备份）：`tasks` 36 行、`customers` 6 行、`log_time` 49 行、`app_sequence` 1 行且值为 37、`app_revision` 1 行且值为 286。不能把只读基线称为可恢复备份。

## 三、根因分析：为什么一个普通更新变成反复阻塞

1. **目标错位**：助手围绕「一定要完成 GitHub→Sites 发布流水线」推进，没有优先确认原站点是否已经满足用户需求。技术任务被误当成业务目标。
2. **线性流程过度强制**：原 Skill 的 PRD→GitHub→Work→Sites 模板易被执行为每一阶段都必须完成；没有「无需部署／用户确认已满足」的正常退出路径。
3. **未先做平台能力探查**：在向 Work 下达长篇部署指令之前，没有明确核实它是否能编辑原 Site 的**托管源码**或更新已关联源分支；重复指令不可能补足缺失的操作。
4. **将建议性检查升级为一票否决**：完整 D1 恢复演练、非 Owner 登录测试、真实移动设备测试等未按变更风险、用户要求和平台能力分级，导致过度审计阻止最初目标。
5. **GitHub CI 查询方法错误**：传统 Commit Status 空列表 ≠ GitHub Actions 没有跑；Work 错误地否认精确 Head 的成功结果，助手随后才纠正。
6. **跨系统 SHA 概念混淆**：GitHub Merge SHA、Sites 版本来源 SHA、Sites 托管分支 HEAD 是不同对象。`hosting.json` 含正确 `project_id` 和 `d1`，不等于已经证明源码同步存在。
7. **交接过宽、重复劳动**：多次要求 Work 重查已验证的 CI、PR、D1，甚至使用了错误的项目工作区，浪费时间却没有解决缺失的 Sites 编辑入口。
8. **进度表达混乱**：把「GitHub 已完成」「生产 Site 功能用户确认」「GitHub 代码已部署至 Site」「安全测试通过」「待核实」混作单一完成状态，引起不必要的争论。
9. **未及时收敛**：同一平台工具缺口反复出现后，本应明确报告并停止重复交接；而不是继续生成几乎相同的 Work 指令。

## 四、建议直接加入现有 Skill 的强制规则（可作为新第 10 章）

### 10.1 业务结果优先于流水线

- **MUST** 在创建发布计划前确认本次真实终点属于：`需求/代码更新`、`GitHub 合并`、`既有 Site 功能验收`、`必须发布新 Sites 版本`、`问题修复`。
- **MUST** 在现有 Site 项目中先检查是否已经具备用户所需功能，或询问用户是否已确认。不要因为 GitHub 存在未同步提交就自动认定功能缺失。
- 用户明确确认现有 Site 已满足需求，且没有明确要求强制同步代码时：**STOP RELEASE**；只做适当状态记录，不继续尝试源同步、版本保存或部署。
- 用户的功能验收只能标记为 **`User confirmed`**；不能伪造 `Sites deployed`、`Source SHA matched` 或 `Production QA complete`。
- 用户明确要求实际发布时，应尽力执行，但依然不能将工具不支持的操作说成已完成。

### 10.2 独立记录五种状态，不得合并

每次项目更新分别给出：

| 状态维度 | 可用值 | 判定证据 |
|---|---|---|
| Requirement / Functional acceptance | Pending / User confirmed / Tested | 用户原话、功能验收记录 |
| GitHub Code / PR | Branch / Ready / Merged | PR 状态、Commit SHA |
| Automated CI | Passed / Failed / Not checked | **与精确 Head 对应的** Actions Run ID |
| Sites deployment | Deployed / Not deployed / Not requested / Unknown | 原生 Sites 发布版本及部署记录 |
| Sites source mapping | Verified / Unverified / Unsupported | 原 Site 真实源码入口、分支/HEAD 对应关系 |

`Merged` 不代表 `Deployed`；`User confirmed` 不代表 `Merged source running on Site`；`Not deployed` 不代表用户功能不可用。

### 10.3 向 Work 交接前先核实执行能力

- **MUST** 在发送 Work 部署任务前，辨明以下能力是否确实存在：① 原 Site 的编辑/托管源码入口；② 原 Site 的源分支查看与同步；③ 保存版本；④ 发布版本；⑤ 原 D1 的只读访问。
- 如果只有「查看和发布已有版本」，没有「更新当前原 Site 源码」，则**不能声称 Work 能从 GitHub 发布新版本**；直接报告缺少的精确操作。
- **MUST** 固定原 Repo、Source subdirectory、Project ID 和 Site URL；不得误进入其他项目或创建新 Site。
- `sites/react-d1/.openai/hosting.json` 的 `project_id` 只验证**目标项目标识**，不能自动推导 GitHub→Sites 已连接、源码可编辑或自动部署可用。
- 遇到同一工具缺口后，**不得连续生成同质 Work 指令**。只有发现新的工具、权限、源入口或新的用户授权，才重新尝试。

### 10.4 GitHub CI 与合并验证

- **MUST** 先读取 PR 最新 `head_sha`，查找**该 SHA 的 GitHub Actions workflow runs**，再看 jobs、conclusions 和必要测试摘要。
- GitHub 的 legacy combined commit `statuses: []` **不等于** Actions 没有成功运行。不得因单一状态接口返回空列表而阻塞已存在的绿色 Actions 结果。
- 合并前检查 Draft/Ready、mergeable、最新 Head 的真实测试结果与变更内容；合并后记录 Merge SHA。
- 如果 Merge Commit 不同于已测试 Head，应检查两者 diff；**零文件内容差异**可作为源内容一致证据，但仍不能代替 Sites 的实际发布。
- 不得为「想让 Work 通过检查」无意义地改动代码或重新启动测试。

### 10.5 Sites SHA 与发布真实来源

- **MUST** 区分 `GITHUB_HEAD_SHA`、`GITHUB_MERGE_SHA`、`SITES_SOURCE_SHA`、`SITES_DEPLOYMENT_VERSION`；除非平台明确返回同一映射关系，不可互换。
- 旧 Sites Source SHA 不存在于 GitHub 仓库，结论只能是 **`Source relationship unverified`**；不可立即断言连接错误。
- 不得将任意 GitHub SHA 冒充 Sites **当前已连接源码分支 HEAD** 传给版本保存 API。
- **只有**通过原 Site 支持的工具真正保存并发布成功后，才能填写新版 Sites Version/Deployment ID；生成 Work Prompt、GitHub Merge 或只读检查均不算发布。

### 10.6 风险检查按影响分类，不要无故扩大硬阻碍

- **必须阻止**：明显可能覆盖、删除、迁移或暴露生产数据而未获适当授权；Site/DB/Owner-only 目标不明；所需发布工具无法访问；必需回归测试失败；无法证明待发布代码的来源。
- **应记录、按风险决定是否单独排期**：无 Schema Migration 的普通功能调整中，平台未暴露完整备份工具；非关键的长期监控、完整灾难恢复演练、尚未约定为发布条件的实体手机测评。不得自行承诺这些无风险，也不得一律变成强制停止项。
- **MUST** 说明只读 D1 行数、字段快照及完整离线备份是不同证据；未执行备份/恢复，必须明确记为未完成。
- 对已有 Site 且用户确认无需额外发布的情况，不应启动任何生产变更，仅保留未完成的运维风险记录。

### 10.7 阻塞处理必须收敛（Blocker Convergence）

每个阻碍只记录五项：**具体缺失的操作／已尝试的工具与实际错误／可以完成的部分／真正需要的权限或平台能力／用户是否仍需该操作**。

- 同一阻碍重复出现且没有新事实：**停止重复交接**，不要换一种措辞重新布置同样任务。
- 工具不可用时只报告**能力缺口**，不要无限生成「请 Work 继续」的指令。
- 用户改变目标、确认功能已满足或决定不发布时：**立即重算任务剩余范围**，不再维持过期的发布阻塞清单。
- 需用户决策时只问一个会真正改变下一步的问题；不要重复索取已提供的信息。

### 10.8 GitHub 收尾状态与 Issue 处理

- **MUST** 在 `docs/PROJECT_STATUS.md` 顶部追加最新有效结论，保留旧尝试为历史；明示旧部署阻碍何时被用户决定所取代。
- 已取消的额外部署工作可使用 `Not Planned` 关闭专门的发布 Issue，**不得**标记为 `Completed / Deployed`。
- 如果 Issue 同时包含仍未验收的安全、备份或手机测试，关闭时须注明这些部分**未验证**，如将来确有需求再单独跟踪。
- 持续性运维风险可保留为独立 Follow-up，不自动重新激活已取消的发布任务。
- 发布报告必须清楚分开：`GitHub verified`、`Sites verified`、`Work reported`、`User confirmed`、`Not verified`。

## 五、可用于测试新版 Skill 的回归场景

| 输入场景 | 正确行为 | 应避免的错误 |
|---|---|---|
| 「网站现在已经有我要的功能」 | 用户确认可用；停止非必要部署，更新状态。 | 强迫继续 GitHub→Sites 同步。 |
| 「PR 的旧 Commit Status 没有 checks」 | 查精确 Head 的 Actions Runs 和 job 结论。 | 直接判定 CI 未通过。 |
| 「PR 已合并，但 Sites Version 仍是旧版」 | 分别记录 GitHub 合并与 Sites 状态；确认是否真的需要更新。 | 把 GitHub Merge 说成已上线。 |
| 「Work 没有编辑原 Site 托管源码工具」 | 记录缺失的具体 API/操作并收敛；停止重复交接。 | 连续给 Work 十份等价部署指令。 |
| 「Sites Source SHA 在 GitHub 找不到」 | 标记映射待核实，寻找真实平台证据。 | 宣称绑定一定错误。 |
| 「hosting.json 已包含 project_id」 | 仅认可目标 Site 标识。 | 宣称具有完整源码同步能力。 |
| 「D1 有行数快照，没有备份工具」 | 区分基线与备份；根据是否存在数据变更风险决定后续。 | 伪称已经备份；或无条件施加全部灾难恢复演练。 |
| 「用户只要求更新 GitHub 项目状态」 | 只修改必要文档与 Issue，保留原站点。 | 自动发起新发布。 |
| 「Work 在错误项目工作区」 | 纠正精确 repo、path、project ID；只交接未完成步骤。 | 在其他项目继续操作或重做开发。 |

## 六、对现有 Skill 的具体修改建议

目标文件：`skill_PRD_GitHub_Sites_Update_2026-10-24.md`（现有 Skill `prd-github-sites-update`，文件头版本 1.1）。

1. **原文保留**：Chat First、用户确认、GitHub commit/PR 真实验证、Sites 不得虚报上线、保留 URL/D1/Owner-only 等安全原则。
2. **第 1 节「目标与执行边界」**：增加「用户目标判定」和「Site 已满足需求可直接结束」的出口；默认流程不是硬性每次走完所有阶段。
3. **第 3 节「启动时检查」**：增加「当前 Site 是否已有所需功能」「Work/Sites 目标源码编辑工具是否存在」「精确 Head Actions CI」三项。
4. **第 6 节「Work 指令」**：先做工具能力检查，交接最小可操作范围；如果 Work 无法操作原 Site 源码，不得声称换 Prompt 可以解决。
5. **第 8 节「验收清单」**：增加 `User confirmed — no deploy needed`、`Sites source mapping unverified`、`CI Actions verified for exact head`、`No new Sites version published` 等可选但明确的状态。
6. **新增第 10 节**：将本文件第四部分（10.1～10.8）的规则并入 Skill；它们应覆盖与之冲突的旧模板内容。
7. **版本说明**：更新 Skill `version`、`updated` 和 change log，记录本次复盘的真实来源；**不要直接覆写旧 Skill 并假称已安装生效**。

## 七、成功判定标准（下次不再重演）

更新后的 Skill 需要做到：

- **结果正确**：用户现有 Site 满足需求时，AI 能结束任务，不再强制部署。
- **状态真实**：GitHub 合并、Sites 发布与用户功能确认三个概念永不混淆。
- **工具真实**：先确认 Sites 编辑/源码同步能力，不能靠反复改 Prompt 假定工具出现。
- **检查准确**：查最新 Head 的 GitHub Actions，而不是依赖空的传统 Commit Status。
- **风险适度**：不跳过真正会损坏数据的高风险保护，但也不凭空添加无关发布门槛。
- **交接最小**：Work 仅做 Chat 完不成且工具明确支持的剩余步骤。
- **阻塞可终止**：同一工具缺口不重复交接；用户更改目标后立即更新任务边界。
- **收尾可审计**：Github 记录分清 `User confirmed`、`GitHub verified`、`Work reported` 与 `Not verified`。

---

**本文件是 Skill 升级建议与 2026-11-05 事实复盘，不是针对生产环境的部署批准。此文件没有修改 Sites、D1 或 Skill 本体。**