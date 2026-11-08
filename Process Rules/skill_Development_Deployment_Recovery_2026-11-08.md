---
name: development-deployment-recovery
description: 用户要求建立或执行开发、测试、发布的故障恢复与断点续跑机制时使用。
version: '2.0'
updated: '2026-11-09'
---

# Development & Deployment Recovery — 自动恢复与可验证交付

> **按需启用**：当用户明确要求恢复失败的开发/部署任务、制定自动恢复 SOP、安装 CI/Checkpoint/Manifest，或当前 Sites 部署已经失败需要恢复时读取。本文件不意味着 GitHub Actions、Sites 自动部署接口或测试环境已经存在。通用安全边界见仓库根目录 `AGENTS.md`。

## 0. 跨项目开发、测试与部署 SOP v2

本 SOP 适用于 React、Next.js、Node.js、Cloudflare Worker、GitHub、ChatGPT Sites 等项目，但具体命令和发布机制必须依据目标仓库的真实技术栈。**Chat First, Work Last；Historical Success First；Risk-Based Testing；Cloud First；Resume from Checkpoint；Verify Real Results。**

**主动生成 Work 指令**：只要用户目标包含 Sites 发布/更新/上线，Chat 完成必要开发、测试和 GitHub 工作后，必须主动在当前 Chat 输出完整可复制的 Work 部署指令，**不得等待用户再次提醒**。若 Chat 已能合法完成实际发布，则以实际发布结果为准；不把 Work 指令当成发布成功。不自动转交 Work，不要求 Work 重新开发或重测已验证功能。若有阻塞，记录真实 checkpoint，仅交接 Work 可执行的剩余步骤。

**责任划分**：AI 在已授权工具范围内负责需求、PRD、UI、代码、GitHub、CI、测试环境选择、测试、诊断、故障恢复、部署和线上核验；用户负责业务需求、重大范围决策、不可逆风险批准、平台必要授权和最终验收。优先使用已连接的云端工具，不默认要求用户安装 Node、Chromium、Playwright、Git、Docker，或手动执行 Git 命令、配置测试环境、处理 DNS、复制构建产物。不能绕过平台授权，也不能凭空声称工具可用。

**历史成功优先**：对已成功部署的项目，先核对上次 Production Version、GitHub Source Commit、Site Source Commit、实际构建方式、部署入口、Deployment ID、线上验证结果；优先复用可证实的成功路径。不得未经调查就重建 Site、修改 URL、重置数据库、改变 Owner-only、替换 Secrets 或迁移平台。历史结果只能作为证据，不能反推编造内部 API/命令。

**智能测试**：先确认源 SHA、依赖、构建配置和测试覆盖是否变化。证据有效则复用已通过的测试；变化时按风险执行最小必要 Node、Worker、Chromium/Playwright、响应式、财务计算、API、数据或权限测试。优先查现有 GitHub Actions 和浏览器测试 artifacts，再找其他已授权云端测试环境；单一环境缺依赖不代表所有测试不可做。失败时区分代码/测试/依赖/环境，修复后只重跑受影响检查及必要回归；无新证据不重复同一失败操作。默认最多两次有依据的安全恢复尝试。

**云端部署**：确认原 Site、目标 SHA、历史可用发布路径和保护要求；区分 GitHub Commit 与 Site Source Commit，必要时先同步源码、读取真实 Site Source HEAD、构建 Worker（仅项目实际使用时）、保存 Site Version、部署 Production，再核验 URL、版本、Deployment ID（平台提供时）、受影响功能、权限和数据保护状态。Save Version ≠ Deploy；CI Passed ≠ Production Deployed。可能已有副作用的发布操作必须先查实际状态再重试。

**恢复和停止**：按真实证据推进 REQUIREMENTS_CONFIRMED → SOURCE_VERIFIED → IMPLEMENTED → TESTED → COMMITTED → SOURCE_SYNCED（适用时）→ BUILT（适用时）→ DEPLOYED → PRODUCTION_VERIFIED → DONE；用户最终验收另记 USER_ACCEPTED。仅从最后未完成阶段恢复，保留 SHA、测试、Site Version、部署证据和错误类别。达到用户要求的终点即停止，不追加无关测试、文档、审批或基础设施变更。

**已提供的历史成功案例（非当前平台核验）**：用户提供的 Work 执行记录称 `jaronhoh/margin-share-calculator` 的 BUG-UI-005 在 GitHub Source `3542177059ed72a52d71f23e8df2d585991a3cb3`、Site Source `8c1d5ed`、Production Version 2、Deployment ID `appgdep_6af0da067d908191b3b42f37866bead8` 成功发布至 `https://margin-share-calculator.jaron-hoh.chatgpt.site/`，保留 Owner-only，并完成 1440/390/320px 和 Light/Dark 检查。**证据等级：User-provided Work execution report；未经本次实时核验。** 此记录可用于调查同项目后续部署，但不代表其他 Sites 具有相同接口。该项目曾使用 `node tools/build-site-worker.mjs`，不应推广为其他项目的通用命令。

## 1. 目标与责任

用户提出需求、决定重大业务范围、完成最终产品验收；AI 在现有授权和可用工具内负责需求分析、PRD、源码与分支、代码修复、冲突处理、适用的 Node/Worker/Chromium 测试、日志诊断、GitHub、部署证据及安全恢复。用户只在必要的账户授权、生产数据风险或不可逆决策时介入。**一次工具失败不等于任务失败；不要让用户处理 checkout、依赖安装、测试环境或日志。**

- **Chat**：需求、开发、测试、修复、GitHub 和恢复点维护。安全且可逆的操作自动继续。
- **Work**：仅用户要求 ChatGPT Sites 部署时，在**当前 Chat 输出完整可复制的 Work 指令**，由用户决定是否使用；不得自动切换模式、创建 Work 任务或把开发/测试移交 Work。
- 平台若无可用 API、连接器、权限或部署工具，不得声称能自动部署；生成指令不等于执行或成功。

## 2. 五层防故障机制

| 层 | 何时检查 | 自动动作 | 成功证据 |
|---|---|---|---|
| 环境预检查 | 开始相关阶段前 | 按需检查 GitHub 仓库/分支/SHA、可用工作区、Node/Worker/Chromium 依赖；若本次要求发布，检查 Sites 入口和权限 | 可读取的 HEAD、环境版本或可执行测试、已确认的发布入口 |
| 自动恢复 | checkout 不存在、会话中断、网络瞬断 | 从 GitHub 重新读取目标 SHA、重新建立临时工作区、检查已完成的 Commit/CI，跳过已通过阶段 | 恢复后 SHA 与 checkpoint 一致 |
| 测试隔离 | 测试失败 | 分类代码失败、测试失败、依赖/环境故障；修复可恢复的依赖/启动问题，再跑受影响测试 | 真实测试命令、退出码、日志、重测结果 |
| 部署前检查 | 准备发布 | 验证目标原 Site、待发布来源、必要权限、生产保护和最小发布范围 | Deployment Gate 满足；缺少能力则记录阻塞 |
| 部署后核验 | 发布操作后 | 读取平台发布结果、Deployment ID/版本（若提供）、健康状态、受影响功能和权限 | 真实生产证据；GitHub Commit 不可代替 |

按变更风险选择测试：文案/样式仅局部和相关响应式；交互检查键盘/触摸/Tooltip；财务逻辑检查单元、边界、图表与导出；数据/权限变更扩大到隔离、完整性和回滚。不要把所有任务强制升级为全站测试。

## 3. 可恢复任务状态（Checkpoint）

```text
REQUIREMENTS_CONFIRMED
  -> SOURCE_VERIFIED
  -> IMPLEMENTED
  -> TESTED
  -> COMMITTED
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

**BUG-UI-005 示例**：先核实 `jaronhoh/margin-share-calculator` 的 `2730e7a5` 是否存在、是否包含目标修复、其 CI/测试和是否已上线；只有证据显示仍需改代码才创建新修复分支。该 SHA 是用户提供的恢复候选，**不是已验证通过的版本**。

## 4. 故障分类与有限恢复

| 故障 | 自动处理 | 何时停止 |
|---|---|---|
| 源码/checkout 缺失 | 核对仓库、分支、SHA，重新获取已有源码；不覆盖未提交用户改动 | 无授权访问路径 |
| Node/Worker/Chromium 失败 | 检查日志和依赖；隔离环境/代码故障，修复可逆环境问题并重测 | 缺少不可替代的运行能力 |
| 暂时网络错误 | 对只读、幂等操作有限退避重试（默认最多 2 次）；检查实际结果后再决定 | 连续失败或出现非瞬时错误 |
| GitHub 写入冲突 | 重新读取 HEAD/文件 SHA，保留他人变更并合并；不强推覆盖 | 无法安全合并 |
| Sites 部署错误 | 检查目标、来源、权限、发布状态和健康日志；先诊断后考虑受控重试 | 无新证据、状态不明、风险不可控 |
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

**不等待用户第二次提醒**：只要本次任务的明确终点包含 Sites 部署，Chat 侧准备完成后自动在当前 Chat 输出：目标原 Site、精确 GitHub SHA 或已保存版本、已验证测试与未验证项、部署范围、保留 URL/数据/配置/权限要求、单次发布步骤、生产最小核验、Deployment ID/版本可得性、失败诊断和恢复 checkpoint。**不要命令 Work 重新开发、修复源码或重跑整个测试矩阵。** 用户自行决定是否使用；输出指令不是发布。

## 8. DONE 标准与停止条件

- 代码阶段：实现目标、实际执行适用测试、检查结果、修复失败、复测、提交并核对 SHA；不能运行的检查明确标记 `not run`。
- 部署在范围内：必须另有实际 Sites 发布证据与生产功能/权限核验，才能称为 `PRODUCTION_VERIFIED`。
- 用户最终产品验收独立记录为 `User accepted`；不假设 AI 能替用户签收。
- 如果平台授权、连接器或运行能力是唯一剩余阻塞：保留成果、checkpoint、精确阻塞与下一步，报告 `BLOCKED`，而非 `DONE`。
- 达到用户指定终点后停止，不重复开发、不追逐版本号、不增加无关测试或文档门槛。
