---
name: development-deployment-recovery
description: 用户要求建立或执行开发、测试、发布的故障恢复与断点续跑机制时使用。
version: '2.2'
updated: '2026-11-09'
---

# Development & Deployment Recovery — 自动恢复与可验证交付

> **按需启用**：当用户明确要求恢复失败的开发/部署任务、制定自动恢复 SOP、安装 CI/Checkpoint/Manifest，或当前 Sites 发布预检、同步或部署出现缺口时读取。本文件不意味着 GitHub Actions、Sites 自动部署接口或测试环境已经存在。通用安全边界见仓库根目录 `AGENTS.md`。

## 0. 跨项目开发、测试与部署 SOP v2

本 SOP 适用于 React、Next.js、Node.js、Cloudflare Worker、GitHub、ChatGPT Sites 等项目，但具体命令和发布机制必须依据目标仓库的真实技术栈。**Chat First, Work Last；Historical Success First；Risk-Based Testing；Cloud First；Resume from Checkpoint；Verify Real Results。**

**主动生成 Work 指令**：只要用户目标包含 Sites 发布/更新/上线，Chat 完成必要开发、测试和 GitHub 工作后，若剩余步骤需 Work 且 Work 有经核实的能力，则主动在当前 Chat 输出完整可复制的 Work 部署指令，**不得等待用户再次提醒**。若 Chat 已能合法完成实际发布，则以实际发布结果为准；不把 Work 指令当成发布成功。不自动转交 Work，不要求 Work 重新开发或重测已验证功能。若有阻塞，记录真实 checkpoint，仅交接 Work 可执行的剩余步骤。

**责任划分**：AI 在已授权工具范围内负责需求、PRD、UI、代码、GitHub、CI、测试环境选择、测试、诊断、故障恢复、部署和线上核验；用户负责业务需求、重大范围决策、不可逆风险批准、平台必要授权和最终验收。优先使用已连接的云端工具，不默认要求用户安装 Node、Chromium、Playwright、Git、Docker，或手动执行 Git 命令、配置测试环境、处理 DNS、复制构建产物。不能绕过平台授权，也不能凭空声称工具可用。

**历史成功优先**：对已成功部署的项目，先核对上次 Production Version、GitHub Source Commit、Site Source Commit、实际构建方式、部署入口、Deployment ID、线上验证结果；优先复用可证实的成功路径。不得未经调查就重建 Site、修改 URL、重置数据库、改变 Owner-only、替换 Secrets 或迁移平台。历史结果只能作为证据，不能反推编造内部 API/命令。

**智能测试**：先确认源 SHA、依赖、构建配置和测试覆盖是否变化。证据有效则复用已通过的测试；变化时按风险执行最小必要 Node、Worker、Chromium/Playwright、响应式、财务计算、API、数据或权限测试。优先查现有 GitHub Actions 和浏览器测试 artifacts，再找其他已授权云端测试环境；单一环境缺依赖不代表所有测试不可做。失败时区分代码/测试/依赖/环境，修复后只重跑受影响检查及必要回归；无新证据不重复同一失败操作。默认最多两次有依据的安全恢复尝试。

**云端部署**：确认原 Site、目标 SHA、历史可用发布路径和保护要求；区分 GitHub Commit 与 Site Source Commit，必要时先同步源码、读取真实 Site Source HEAD、构建 Worker（仅项目实际使用时）、保存 Site Version、部署 Production，再核验 URL、版本、Deployment ID（平台提供时）、受影响功能、权限和数据保护状态。Save Version ≠ Deploy；CI Passed ≠ Production Deployed。可能已有副作用的发布操作必须先查实际状态再重试。

**恢复和停止**：按真实证据推进 REQUIREMENTS_CONFIRMED → SOURCE_VERIFIED → IMPLEMENTED → TESTED → COMMITTED → SOURCE_SYNCED（适用时）→ BUILT（适用时）→ VERSION_SAVED → DEPLOYED → PRODUCTION_VERIFIED → DONE；用户最终验收另记 USER_ACCEPTED。仅从最后未完成阶段恢复，保留 SHA、测试、Site Version、部署证据和错误类别。达到用户要求的终点即停止，不追加无关测试、文档、审批或基础设施变更。

## 0A. Last Known Good Deployment — 必须优先复用的 SOP

**目标**：已有 V1/V2 成功部署时，禁止每次从零研究、反复交接或只输出 BLOCKED。先恢复最后一次成功部署的**真实执行链**，而非仅查询当前 Sites API 能否显示 source repo。

1. **找历史证据**：查原 Site 的 V1/V2 Version、Deployment ID、source provenance；查当时 Work 对话执行记录、工具调用、已保存源码、GitHub commit、构建产物与部署结果。区分「成功结果」与「确实使用过的操作方法」；无法访问历史执行日志时如实记录，绝不编造工具或命令。
2. **复现同一路径**：按历史记录确认使用的实际 Sites 编辑/源码同步、构建、Save Version 和 Deploy 操作及其当前可用性；从上次成功 source/产物出发，仅替换本次已验证改动。不要把 GitHub commit SHA 直接冒充 Sites 绑定源码 HEAD。
3. **路径优先级**：先检查现有已保存且可追溯的新版本；再检查 V2 实际使用的原生发布入口；再检查原 Site 已授权的源码/产物同步和浏览器管理入口。每种只检查一次有意义的路径；同一失败没有新条件不重试。
4. **明确缺口**：只有历史执行链确实无法取得或已不可用、其他实际可用路径均排除后，才将**剩余发布阶段**标为 BLOCKED。不能把「某个 Sites 读取接口不返回绑定仓库」直接等同于「所有部署入口都不可用」。
5. **部署验证**：保留原 Site ID/URL、Owner-only、配置和数据；核对同步后 source/产物、保存的新版本、Production Deployment ID、线上功能。GitHub 合并不等于上线。

**Margin Share Calculator 的已知 V2 成功结果（仅结果，不是操作 SOP）**：用户提供的执行报告记载 GitHub BUG-UI-005 SHA `3542177059ed72a52d71f23e8df2d585991a3cb3`、Site Source SHA `8c1d5ed7d04dcda0e97ba435b554d9e13b517dee`、Production Version 2、Deployment `appgdep_6af0da067d908191b3b42f37866bead8`、原 URL `https://margin-share-calculator.jaron-hoh.chatgpt.site/`、Owner-only。曾用 `node tools/build-site-worker.mjs`；**尚无可核实的 V1/V2 逐步源码同步/Save/Deploy 工具调用记录，不能声称完整 SOP 已复现**。追溯到后再将实际步骤补入项目 Release Runbook。

## 0.1 强制防重复失败协议：空工作区 / GitHub DNS / 克隆失败

**强制前置条件（每个新 Work 会话独立执行）**：在尝试任何 `git clone`、终端网络访问或权限申请之前，先读取本 Skill 的 GitHub 最新版并记录版本；读取目标仓库现有 GitHub 连接器的可用操作，直接核对目标分支、源码文件、PRD、历史 CI、提交与上次 Sites 发布证据。远程 Skill 已更新不等于当前 Work 自动加载；如无法读取，明确报告规则未加载，不得假称遵守。

**禁止把空目录当作阻塞**：本地工作目录为空只表示没有 checkout，不等于无法读取仓库。优先通过 GitHub 连接器执行文件读取、搜索、代码修改/提交、PR 审核和 CI 查询；如连接器有权限，不得声称必须先克隆才能查看代码、PRD 或测试文件。连接器无法提供完整本地执行环境时，将构建/运行测试单独标记为待解决，不得因此停止审计和其他可执行工作。

**DNS 熔断器**：一旦 `github.com` DNS 解析失败（例如 `Could not resolve host`），将当前终端路径标记 `DNS_BLOCKED`；**同一任务不得再尝试相同 git clone / ls-remote / curl / 网络提权路径**，除非网络环境或授权状态出现可验证的新变化。不得反复申请同一种网络权限。改用现有已授权 GitHub 连接器；连接器成功读取仓库时，继续可完成的任务。连接器自身失败时，先检查返回的错误类别，不得循环同样调用。

**替代执行路线**：需要运行 Node、Worker、Chromium 等测试时，先核对精确源码 SHA 对应的现有 GitHub Actions、可用的云端执行工具及既有工作区。若允许通过 GitHub 连接器安全修改 workflow 触发必要测试，可按项目现有 CI 规则执行；不得为了测试而修改生产逻辑或虚构通过结果。任何可用的其他环境都必须经过真实能力确认，不能假设存在。

**分项继续，不得整体停工**：把工作拆为 `CONNECTOR_READ`、`AUDIT`、`IMPLEMENT`、`CI_TEST`、`SITES_DEPLOY`、`PRODUCTION_VERIFY`。一个阶段的某项环境依赖失败，先记录该路径失败，继续其他独立且安全的任务；不能因克隆失败直接宣称全部未开始。保留每项真实证据和最后成功 checkpoint。

**禁止伪保证**：Skill 无法更改平台沙箱 DNS、绕过必要授权或保证新会话自动读取。若所有合法执行路径均不可用，明确说明唯一不可替代的阻塞；不要向用户重复提出已经失败且没有新条件的操作。

## 0.2 Sites 绑定源码失败与发布断点续跑

开发前按 [PRD → GitHub → Sites §3.1](skill_PRD_GitHub_Sites_Update_2026-11-05.md#31-既有-sites-发布路径预检开发前) 预检。对 GitHub 已合并、CI 已通过的 Margin Share Calculator 类任务，先验证证据对应目标 SHA，再从未完成的 Sites 阶段续跑。GitHub 可读可写、CI PASS、`hosting.json` 或历史成功记录都无法单独证明当前 Site 的源码绑定、写入能力或上线状态。

1. **判定真实状态**。实时读取原 Site ID、URL、Production 版本、实际绑定源/分支/HEAD 和可用工具。核对已保存版本和部署状态，避免重复同步、Save 或 Deploy。将失败定位到目标识别、source 写入、构建、保存、激活或验收。脱敏记录工具、错误、时间和失败路径。
2. **逐一排查适用备选路径**。优先复用同一 Site 有证据且仍可用的发布路径。若已存在来源可核实且与待发布 SHA 对应的已保存版本，从 Deploy 或 Production 验收继续。否则核对是否可通过原 Site 的受支持源码同步工具或平台提供的限时、限范围凭证，同步到已核实的绑定源。若该路径失败，再检查同一 Site 是否支持从固定 GitHub SHA 构建并经原生入口保存可追溯产物，或通过已授权的官方界面完成同一步。每条路径记录已验证、已排除及理由。界面、凭证、CLI 和 API 只有当前环境实际提供且获授权才可使用，禁止猜测内部接口、复用他人凭证或泄露密钥。
3. **约束写入与重试**。同步前读取目标 HEAD，保留非本次变更，核实源码或产物对应待发布 SHA。同步后回读实际来源。Save 后回读版本、目标和来源，Deploy 后读取激活状态，再做 Production 验收。若入口将 Save 和 Deploy 合并，须在调用前确认两项授权，调用后分别核验证据。结果不明时先查询状态，禁止重复生产写入；无新证据不重试相同路径。发布保持原 Site、URL、数据、配置、Secrets 和访问权限，生产验收以获授权的只读检查为限。新增或替换 Site、改绑源、权限放宽、生产数据写入或破坏性操作需单独明确授权。
4. **保留 checkpoint，避免循环交接**。分别记录 GitHub SHA/PR、CI 或测试链接、Site ID/URL、绑定源和同步后 SHA 或产物摘要、Save Version ID、Deployment ID/状态（如提供）、线上版本及验收结果。Work 从第一个未完成阶段继续，执行授权的发布准备、诊断和线上核验，复用有效测试，不把部署故障推回 Chat 重做开发或测试。仅在另一环境具有经核实的不同能力且能推进剩余步骤时交接一次，附 checkpoint 与已排除路径。
5. **停止条件**。只有全部适用合法路径均已验证失败或不可用，或剩余动作确实需要用户新增授权，才将该阶段标记 `BLOCKED`。继续其他独立且已授权步骤，报告最后成功阶段、具体证据、各路径排除理由以及最小下一步。若线上仍是旧版本或无法核验，分别如实记录。达到请求的验收终点即停止。

## 1. 目标与责任

用户提出需求、决定重大业务范围、完成最终产品验收；AI 在现有授权和可用工具内负责需求分析、PRD、源码与分支、代码修复、冲突处理、适用的 Node/Worker/Chromium 测试、日志诊断、GitHub、部署证据及安全恢复。用户只在必要的账户授权、生产数据风险或不可逆决策时介入。**一次工具失败不等于任务失败；不要让用户处理 checkout、依赖安装、测试环境或日志。**

- **Chat**：需求、开发、测试、修复、GitHub 和恢复点维护。安全且可逆的操作自动继续。
- **Work**：仅当用户要求 ChatGPT Sites 部署、当前 Chat 无法完成且 Work 具备经核实的能力时，在**当前 Chat 输出完整可复制的 Work 指令**，由用户决定是否使用；不得自动切换模式、创建 Work 任务或把开发/测试移交 Work。
- 平台若无可用 API、连接器、权限或部署工具，不得声称能自动部署；生成指令不等于执行或成功。

## 2. 五层防故障机制

| 层 | 何时检查 | 自动动作 | 成功证据 |
|---|---|---|---|
| 环境预检查 | 开始相关阶段前 | 按需检查 GitHub 仓库/分支/SHA、可用工作区、Node/Worker/Chromium 依赖；若本次要求发布，开发前按 §0.2 核验整条 Sites 路径 | 可读取的 HEAD、环境版本或可执行测试、已确认的发布入口 |
| 自动恢复 | checkout 不存在、会话中断、网络瞬断 | 从 GitHub 重新读取目标 SHA、重新建立临时工作区、检查已完成的 Commit/CI，跳过已通过阶段 | 恢复后 SHA 与 checkpoint 一致 |
| 测试隔离 | 测试失败 | 分类代码失败、测试失败、依赖/环境故障；修复可恢复的依赖/启动问题，再跑受影响测试 | 真实测试命令、退出码、日志、重测结果 |
| 部署前检查 | 准备发布 | 验证目标原 Site、待发布来源、必要权限、生产保护和最小发布范围 | Deployment Gate 满足；缺少能力则按 §0.2 核验备选路径 |
| 部署后核验 | 发布操作后 | 读取平台发布结果、Deployment ID/版本（若提供）、健康状态、受影响功能和权限 | 真实生产证据；GitHub Commit 不可代替 |

按变更风险选择测试：文案/样式仅局部和相关响应式；交互检查键盘/触摸/Tooltip；财务逻辑检查单元、边界、图表与导出；数据/权限变更扩大到隔离、完整性和回滚。不要把所有任务强制升级为全站测试。

## 3. 可恢复任务状态（Checkpoint）

```text
REQUIREMENTS_CONFIRMED
  -> SOURCE_VERIFIED
  -> IMPLEMENTED
  -> TESTED
  -> COMMITTED
  -> SOURCE_SYNCED (if applicable)
  -> BUILT (if applicable)
  -> VERSION_SAVED
  -> DEPLOYED
  -> PRODUCTION_VERIFIED
  -> DONE
```

每个阶段只能根据实际证据晋级；用户未要求部署时，任务可以在 COMMITTED/TESTED 等约定终点完成，不得虚构后续状态。推荐在**目标应用仓库**使用 `docs/release/recovery-checkpoint.json`，若项目已有等效文件则复用；禁止把个人 token 或敏感环境变量写入仓库。

建议字段：`task_id`, `target_repo`, `source_branch`, `source_sha`, `stage`, `stage_status`, `last_successful_stage`, `test_evidence`, `failure_category`, `failure_summary`, `retry_count`, `next_safe_action`, `blocker`, `updated_at`。使用有效 JSON；不把敏感日志原文存入公开文件。

恢复算法：
1. 读取最新用户目标、目标仓库和 checkpoint；核对 GitHub 真实 SHA/PR/CI 与 Sites 证据，防止陈旧状态覆盖现实。
2. **先查已有成果，再创建新分支或修改代码**。若代码已提交且测试通过，从未完成的部署/验证阶段恢复。
3. 已通过的测试仅在 SHA、依赖、配置和受影响范围未改变且证据仍有效时复用；否则只重跑必要检查。
4. 每完成一个有意义阶段更新 checkpoint。若无法写回仓库，在当前对话保留结构化恢复摘要，不声称持久化成功。
5. 外部阻塞时保留 SHA、最后成功阶段、错误证据和唯一下一动作；下次不重头开发。

## 4. 故障分类与有限恢复

| 故障 | 自动处理 | 何时停止 |
|---|---|---|
| 源码/checkout 缺失 | 核对仓库、分支、SHA，重新获取已有源码；不覆盖未提交用户改动 | 无授权访问路径 |
| Node/Worker/Chromium 失败 | 检查日志和依赖；隔离环境/代码故障，修复可逆环境问题并重测 | 缺少不可替代的运行能力 |
| 暂时网络错误 | 对只读、幂等操作有限退避重试（默认最多 2 次）；检查实际结果后再决定 | 连续失败或出现非瞬时错误 |
| GitHub 写入冲突 | 重新读取 HEAD/文件 SHA，保留他人变更并合并；不强推覆盖 | 无法安全合并 |
| Sites 部署错误 | 按 §0.2 诊断、核查状态并尝试合法备选路径 | 全部适用路径排除或需要新增用户授权 |
| 权限拒绝 | 检查已有合法连接/授权范围；说明缺失的最小权限 | 需要用户或平台批准；禁止绕过 |

**重试安全性**：只读/幂等步骤可有限重试；GitHub 写入和 Sites 发布可能已生效，**必须先查询结果并防止重复副作用**。不得对未知状态的生产发布机械重试。若新证据表明原已保存版本可发布、平台恢复或用户明确授权，可做一次受控重试并验证，不将第一次失败永久视为不可恢复。

## 5. Release Manifest 与部署验证

在目标应用仓库按需维护 `docs/release/release-manifest.json`，复用现有格式优先。建议记录：
- `task_id`、`source_repo`、`source_sha`、`source_branch`、`ci_run_url`、`tests`（命令/状态/环境/时间）。
- `target_site`、`deployment_id`、`platform_version`、`deployed_at`、`health_status`、`production_checks`、`access_check`。
- `verification_status` 分别标记 `GitHub verified`、`CI verified`、`Sites saved`、`Sites deployed`、`Production verified`、`User accepted`；未知写 `unknown`，不可推导。

发布前 Gate：确认用户确实要求部署、原 Site URL/ID、来源 SHA 或已保存版本、Chat 测试证据、目标功能、数据/配置/权限保护、可用发布工具、成功标准。发布后必须实际核验；若 Sites 不提供 Deployment ID、版本或生产访问接口，明确记录 `unavailable`，不得捏造。

## 6. GitHub Actions CI 落地规则

当用户要求**在目标应用仓库安装 CI** 时，先读取真实技术栈和现有 workflow，复用/扩展已有配置，不在 Skill 仓库凭空生成通用的假测试工作流。

- Node：按实际 `package.json` scripts 运行 install、lint/typecheck、unit/integration；锁定包管理器及 lockfile。
- Worker：只在项目确有 Worker/对应测试命令时执行；需要外部服务则使用安全隔离的测试配置。
- Chromium：仅在项目有浏览器测试时配置 Playwright/Puppeteer 和相应浏览器依赖；保留截图/trace/log artifact，区分启动失败与断言失败。
- CI 必须关联精确 Commit SHA，避免把其他分支/旧 workflow 的绿灯当成当前提交通过；失败后修复并验证。
- Actions 能自动运行 CI，不意味着 Sites 具有自动部署接口。不要将 GitHub merge/commit 视为生产发布。

## 7. Work 部署指令输出模板（仅在用户要求发布时）

**不等待用户第二次提醒**：只要本次任务的明确终点包含 Sites 部署，Chat 侧准备完成且 Work 有经核实的能力执行剩余步骤时，在当前 Chat 输出：目标原 Site、精确 GitHub SHA 或已保存版本、已验证测试与未验证项、部署范围、保留 URL/数据/配置/权限要求、单次发布步骤、生产最小核验、Deployment ID/版本可得性、失败诊断和恢复 checkpoint。**不要命令 Work 重新开发、修复源码或重跑整个测试矩阵。** 用户自行决定是否使用；输出指令不是发布。

## 8. DONE 标准与停止条件

- 代码阶段：实现目标、实际执行适用测试、检查结果、修复失败、复测、提交并核对 SHA；不能运行的检查明确标记 `not run`。
- 部署在范围内：必须另有实际 Sites 发布证据与生产功能/权限核验，才能称为 `PRODUCTION_VERIFIED`。
- 用户最终产品验收独立记录为 `User accepted`；不假设 AI 能替用户签收。
- 如果全部适用合法路径均已排除，或剩余步骤需要用户新增授权：保留成果、checkpoint、精确阻塞与下一步，报告 `BLOCKED`，而非 `DONE`。
- 达到用户指定终点后停止，不重复开发、不追逐版本号、不增加无关测试或文档门槛。


## v3 故障决策补充（优先于本文件较早的模糊表述）
DNS 熔断以 `(hostname, execution environment, authorization state)` 为键，不得把 `github.com` 的失败推断为 `git.chatgpt-team.site` 失败。相同条件下不重复 clone/curl/权限申请；环境、授权或网络状态发生可验证变化时，允许一次有界、带超时的受控复测。连接器可用则继续独立阶段。仅在所有适用合法路径排除或确需新增授权时，才将具体阶段标记 BLOCKED，附最后成功 checkpoint、排除证据及最小恢复动作。历史 V3/V4 成功记录须分别保留；V4 用户发起的 Yahoo API 恢复不代表 Payment Date、CSV/JSON 均已独立验证。正常部署步骤由 Sites Skill 统一定义，本 Skill 只补充故障处理与恢复。
