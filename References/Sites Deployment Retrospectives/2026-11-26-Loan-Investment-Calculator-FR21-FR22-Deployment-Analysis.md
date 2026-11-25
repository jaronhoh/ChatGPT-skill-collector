# Loan Investment Calculator — FR-21 / FR-22 部署综合复盘

> Reference only / 非执行 Skill。整理自用户提供的《ChatGPT Sites Deployment — 部署复盘与 SKILL.md 优化需求报告》v1.0（2026-11-26）。原报告汇总四份复盘，并未重新核实全部原始日志。本档案不构成部署授权，不修改 AGENTS.md 或 SKILL.md，不保证历史接口或权限仍可用。

## 1. 案例背景
- 项目：Loan Investment Calculator；GitHub: jaronhoh/loan-calculator；main；development/index.html。
- 历史 Sites Project ID: appgprj_6ad0f21a67c88191ba4abfa0761bc3c0；URL: https://loan-investment-calculator.jaron-hoh.chatgpt.site/；Owner Only；cloudflare_artifact。
- 原报告参考 AGENTS v4.1 / SKILL v4.4；优化建议尚未实施。项目参数仅供历史定位。

## 2. 四个案例（原报告转述，非本次独立验证）

| ID | 原始复盘文件 | 结果与证据 |
|---|---|---|
| A | FR21_本对话部署过程详细复盘.md | 失败；Connector、Sites API、短期认证及测试可用；Node VM 约 80,436 项检查；Helper 等待/中断及审批未完成；无新 Sites commit、artifact 或生产发布。没有明确 DNS 错误记录。 |
| B | FR21-deployment-retrospective.md | 失败；Connector 正常、已有测试通过；Sandbox Git 报 Could not resolve host: github.com / git.chatgpt-team.site；checkout 未完成，审批未完成，无新生产发布。 |
| C | loan-calculator-deployment-retrospective.md | 成功；初始网络问题后获得所需权限，完成源码同步、测试、Sites commit、artifact、部署及 QA；V15 FR-21、V16 Bilingual Footer Update。 |
| D | FR-22-Sites-Deployment-Retrospective.md | 成功；恢复认证过期、403、archive 结构、stale SHA、artifact 不匹配及 Git 同步问题；V17、V18、V19。 |

原始四份复盘文件未随本次报告提供，不能视为已独立核实。五次成功发布是报告所述历史结果，不是当前生产状态。

## 3. 观察与证据边界
- 用户观察：现有 Chat 延续到 Work 的案例成功；复制指令到独立 Work 的案例失败。样本为两个成功、两个失败案例，不能证明启动方式决定权限策略，也不能保证未来成功。
- Connector 与 Sandbox Git 是不同执行路径；前者正常不证明后者网络可用，Sandbox DNS 失败也不证明 GitHub/Sites 服务整体不可用。
- Sandbox 指 Work 执行环境，不是用户 Windows 电脑。
- 用户纠正：aborted by user 是未及时响应审批而超时，并非主动拒绝。具体平台机制未知；0.1–0.8 秒工具计时不能当作完整 UI 审批窗口。
- 用户提到 note.pl；原报告推测可能是 Node.js helper（如 site-workflow.mjs），实际名称未确认。审批可能来自网络、文件或子进程权限，而非 Node.js 本身。
- Business Authorization 与 Platform Execution Permission 不同；一次部署授权不覆盖平台强制审批。任务级/会话级权限是否支持未确认。

## 4. 历史恢复经验（非可直接执行的操作手册）

| 历史问题 | 报告中的恢复经验 / 边界 |
|---|---|
| Token 过期、认证导致的 403 | 通过当时授权机制重新认证；不把所有 403 都归因于过期 |
| Stale Sites SHA | 重新核对 Sites Remote HEAD；区分 GitHub application SHA 与 Sites SHA |
| Artifact / commit mismatch | 对应正确 Sites commit 生成并验证 artifact |
| Archive / manifest 错误 | 按实际 hosting configuration 检查；历史路径 .openai/hosting.json、dist/index.html 不是通用要求 |
| Git 同步错误 | 核对执行路径及权限；使用实际可用的授权方式 |
| DNS 错误 | 区分 hostname、环境及网络权限；不重复相同失败操作 |
| 审批失败 | 区分明确拒绝、超时、自动拒绝、机制不可用、权限过期、未知失败 |
| 权限明确不可用 | 无授权替代路径时停止并报告具体 blocker |
| 生产版本不符 | 核对实际 active version 与 release mapping |

## 5. 原报告提出的优化方向（未实施）
- DEP-01–05：明确部署触发、复用业务授权、优先历史成功路径、优先 Chat-to-Work（仅观察支持）、GitHub 源码基线。
- DEP-06–11：复用有效测试、减少 Sandbox/Node.js、平台支持时集中及复用权限、正确解释审批失败。
- DEP-12–16：认证刷新、Sites SHA、artifact 验证、可恢复错误处理、禁止相同失败循环。
- DEP-17–20：生产状态验证、必要验收、准确 blocker、保留安全控制。
- 原报告建议的顺序：识别目标与 release → 取得源码 → 复用有效测试 → 认证 → 同步 Sites 源码 → 核对 Sites SHA → artifact → 验证对应关系 → 发布 → 核对 active version → QA → 记录结果。此为历史建议，不是当前执行指令。
- 测试复用需对应实际 release commit、有效范围及未失效证据；相关变更或必要验证仍需测试。提交、测试、artifact、部署请求与实际生产成功分别记录。
- 原报告排除新 GitHub Actions、CI/CD 重构、新 hosting、A/B 实验及重复有效测试。
- 原报告 AC-01–20 属于拟议 Skill 修改验收条件，未执行、未验收；本次归档不恢复 Sites Skill。

## 6. 未来比较案例时的证据字段
Application、Release、GitHub SHA、Sites SHA、active production version、deployment status、test result（reused/executed/not verified）、production QA、URL、blocker。原报告未提供每次发布的具体 SHA 或 deployment ID，不补造。

## 7. 归档结论
历史记录支持减少重复操作、区分权限和业务授权、核对源码与 artifact、避免误判审批超时。不能由此推断当前平台内部审批规则、权限持久性、无 Sandbox 部署能力或确定的 Work 启动因果关系。现行根规则仍明确排除 Sites 部署 Skill；原报告提出的修改请求仅作为历史提案保留。本次没有生产操作或新测试。
