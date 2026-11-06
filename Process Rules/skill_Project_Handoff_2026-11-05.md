# Skill — Project Progress, Release Tracking & Cross-Conversation Handoff

**Skill ID:** `project-handoff`  
**Version:** 1.1  
**Created:** 2026-10-16  
**Updated:** 2026-11-05  
**Language:** Simplified Chinese by default; preserve project terminology and file naming conventions.  
**Scope:** Any software project, including ChatGPT Sites, Codex, GitHub, and projects with separate development and production environments.

## 1. Purpose

Maintain a durable, auditable **single source of truth** for project progress, releases, blockers, and handoff across different ChatGPT conversations, tools, devices, and collaborators. **Do not rely on conversation memory alone.**

This skill governs **project status and delivery records**. It does not replace PRD/FDD, source code, issue trackers, or deployment documentation.

## 2. When to activate

Activate this skill whenever the user asks to:
- Start, resume, continue, or hand off work on a project.
- Check project progress, ask “update?”, or report a completed task.
- Implement a change, run tests, create/merge a PR, or publish/update a Site.
- Prepare a release, verify production data, or coordinate work across conversations.

## 3. Repository convention

For each project, keep one canonical Markdown status file:

`docs/PROJECT_STATUS.md`

If the project already has an equivalent file, **reuse it** rather than creating a duplicate. If the repository uses a different documentation structure, follow the established convention and record the canonical path in the README or project instructions.

**Do not add a `skills/` or `templates/` directory to a project repository solely for this skill.** The skill file may be stored separately in the user's skill collection; project repositories only need their status file.

## 4. Mandatory startup / handoff procedure

1. **Identify the project**: repository, branch, production Site (if any), and relevant environment. If ambiguous, ask for the minimum missing identifier.
2. **Read the canonical status file from the live repository**, normally `main/docs/PROJECT_STATUS.md`. Do not answer from old chat context alone.
3. **Cross-check live evidence** where tools allow: current branch/HEAD, open PRs, recent commits, CI test runs, and relevant deployment details.
4. **Distinguish evidence levels** for each assertion:
   - `GitHub verified` — directly checked in repository/PR/CI.
   - `Sites verified` — directly checked using a Sites management/deployment tool.
   - `User reported` — explicitly supplied by the user, not independently verified.
   - `Not verified` — evidence missing or tool access unavailable.
5. **Detect discrepancies** between the status file, GitHub, Sites, and user reports. Explain them; do not silently overwrite a newer fact with an older one.
6. Summarize **current state, last successful release, outstanding blockers, and next action** before making changes.

If a tool cannot access GitHub/Sites, **state the limitation** and do not claim a successful read, deployment, or verification.

## 5. Required update triggers

Update `docs/PROJECT_STATUS.md` **whenever any of these occur**:
- PRD/FDD requirement or scope approved or materially changed.
- Feature branch, implementation milestone, or PR created.
- Test run completed, failed, or was fixed; include links and results.
- PR merged; record merge commit and target branch.
- Deployment initiated, succeeded, failed, or rolled back.
- Production verification completed, especially record counts, database integrity, configuration, and access permissions.
- Blocker, risk, dependency, or next owner/action changes.

**Important:** Write a factual status entry after the action succeeds. Do not mark a planned action as completed. If repository writes fail, say so and provide the Markdown entry for manual application.

## 6. Standard delivery workflow

`Requirements/PRD → Branch → Implementation → Tests → PR Review → Merge → Deployment → Production Verification → Status Update`

For Sites projects:
- **GitHub merge ≠ Sites deployment.** Track these separately.
- Prefer updating the **existing Site** unless the user explicitly requests a new one.
- Preserve production data, identifiers/sequences, Site URL, configuration, and access permissions unless the user explicitly authorizes changes.
- Record deployed **Site version, source commit, deployment result, timestamp, and verification evidence**.
- Do not invent Sites capabilities or deployment links.

For projects without GitHub, use the team's durable versioned source of truth, but keep the same evidence and update rules.

## 7. Canonical `PROJECT_STATUS.md` structure

Create the file using this structure when no equivalent exists:

```md
# <Project Name> — Project Status & Handoff

> Single source of truth for cross-conversation handoff.
> Last updated: YYYY-MM-DD HH:mm (timezone)

## Project identity
- Repository:
- Default branch:
- Canonical status file: `docs/PROJECT_STATUS.md`
- Production Site / environment:
- Maintainer / owner (if known):

## Current status
| Area | Status | Evidence | Updated |
| --- | --- | --- | --- |
| Requirements / PRD | Not started / In progress / Completed | Link | Date |
| Development | ... | Branch / commit | Date |
| Automated tests | ... | CI run / results | Date |
| Pull request / merge | ... | PR / merge commit | Date |
| Production deployment | ... | Version / deploy result | Date |
| Production verification | ... | Counts / checks | Date |

## Latest release
- Version:
- Source commit:
- Release date/time and timezone:
- Deployment status:
- Production URL:
- Scope of changes:
- Test summary:
- Data / configuration / permissions verification:
- Evidence classification: GitHub verified / Sites verified / User reported / Not verified

## Open work and blockers
| Priority | Item | Owner | Next action | Evidence |
| --- | --- | --- | --- | --- |

## Handoff notes
- Important design decisions:
- Constraints / do-not-change items:
- Relevant PRD/FDD/test/deployment paths:
- Known issues / risks:
- Next recommended action:

## Change log (append-only)
| Date/time | Change | Result | Commit / PR / deployment | Evidence source |
| --- | --- | --- | --- | --- |

## Next update
- Trigger:
- Responsible party (if known):
```

Use actual facts; write `Unknown` or `Not verified` rather than guessing. Never store passwords, API keys, tokens, or other secrets in the status file.

## 8. Change-log discipline

- **Append** new dated entries; do not erase history to make the project appear complete.
- Keep the top-level status concise; move detailed specifications to PRD/FDD and link them.
- Link commits, PRs, test runs, releases, and incident reports when available.
- Separate `Completed`, `In progress`, `Blocked`, `Failed`, and `Not verified`.
- Distinguish production deployment from production validation.
- Record the **actual environment** (development, staging, UAT, production).
- Use the project's agreed timezone (default to the user's specified timezone; otherwise explicitly label timestamps).
- Preserve the latest verified release even when newer development is ongoing.

## 9. Update and commit protocol

When GitHub write access is available:
1. Fetch the latest status file and its SHA from the target branch.
2. Integrate the new facts without deleting unrelated history.
3. Commit the change with a clear message such as `docs: update project status after v4 release`.
4. Re-read or verify the resulting commit/file when possible.
5. Report the status file link and commit SHA.

Prefer updating the status file as part of the same PR for development changes, then add the **post-merge / post-deployment** entry on the default branch once those actions actually occur. If concurrent edits are detected, re-fetch and reconcile before retrying.

## 10. Response format for progress checks

When asked for an update, respond briefly with:

1. **Project / environment / last verified release**.
2. **Status table** (requirements, code, tests, PR, deployment, verification).
3. **Changes since last update**, with evidence.
4. **Blockers and next action**.
5. **Canonical GitHub status-file link** and whether it was updated during this interaction.

Never claim that a status file was updated unless a repository write succeeded.

## 11. Ready-to-use handoff instruction

> 请先读取当前项目 GitHub 默认分支的 `docs/PROJECT_STATUS.md`，并检查最新 Commit、PR、CI 测试及 Sites 部署状态。以该文件作为跨对话交接的主要记录，但须与实时证据核对。区分 GitHub 已验证、Sites 已验证、用户报告及未验证的状态。每次完成开发、测试、合并、部署或生产验证后，必须更新同一份 `PROJECT_STATUS.md` 并追加 Change Log；保留历史记录、现有网站、数据、配置及访问权限。若工具无法读取或写入，请明确说明，不得假装完成。

## 12. Completion checklist

- [ ] Read latest canonical status file.
- [ ] Checked live repository/CI/deployment evidence where available.
- [ ] Identified current release, source commit, and next action.
- [ ] Preserved production data, settings, URL, and permissions.
- [ ] Updated status table and append-only change log.
- [ ] Classified each important claim by evidence source.
- [ ] Committed the status file or clearly reported why it could not be committed.
- [ ] Shared the canonical file link and commit SHA (if committed).

## 13. 业务结果、五维状态与阻塞收敛（2026-11-05 复盘补充）

- **业务结果优先**：交接前确认当前用户目标和终点是文档/代码更新、GitHub 合并、现有 Site 功能验收还是新版本发布。用户确认现有 Site 已满足且无需额外发布时，将额外部署标记 Not Planned，不继续发布；保留历史尝试。
- **五维状态独立记录**：① Requirement / Functional acceptance（Pending / User confirmed / Tested）；② GitHub Code / PR（Branch / Ready / Merged）；③ Automated CI（Passed / Failed / Not checked，附精确 Head 的 Actions Run）；④ Sites deployment（Deployed / Not deployed / Not requested / Unknown）；⑤ Sites source mapping（Verified / Unverified / Unsupported）。不得以 Merged 推导 Deployed，也不得以 User confirmed 推导源 SHA 匹配。
- **证据等级**：GitHub verified、Sites verified、Work reported、User confirmed、Not verified。Work 转述不得写作本聊天独立验证；只读 D1 行数/快照不等于可恢复备份。
- **SHA 边界**：分别记录 GITHUB_HEAD_SHA、GITHUB_MERGE_SHA、SITES_SOURCE_SHA、SITES_DEPLOYMENT_VERSION；旧 Sites SHA 不在 GitHub 中只说明映射未核实，不足以断言绑定错误。
- **阻塞收敛**：每项只记录缺失操作、已试工具及实际错误、可完成部分、需要的权限/能力、用户是否仍需此操作。无新证据不得重复交接；用户变更目标立即重算剩余范围。
- **状态收尾**：在 PROJECT_STATUS.md 顶部呈现最新有效结论，旧尝试保留在追加日志。取消额外发布可将专用 Issue 标记 Not Planned，但不能伪称已部署或已完成尚未验证的安全/移动端/备份检查。


## 2026-11-06 部署事故防复发 SOP（强制；覆盖冲突旧规则）

> 来源：《小改动部署复盘》《ChatGPT Sites 部署事故复盘与防错规范》。适用于既有 Site 的更新。目标是**最小变更、一次受控部署、证据闭环、及时停止**。这是流程规范，不是任何生产部署授权。

### A. 任务分级与终点
- **MUST** 先确定用户实际终点：仅文档／代码／GitHub、现网功能验收、或明确要求发布 Sites。用户不要求部署时，不创建 Work 部署任务。
- **Quick**：一行文案、Footer、样式等局部低风险修改，只验证改动点、页面正常加载及直接受影响的回归；**不得默认执行全站 UAT**。
- **Standard / High-risk**：涉及业务逻辑、权限、数据、迁移、密钥、集成或跨模块变更时，按影响范围扩大测试；高风险操作需授权和可行的备份／回滚方案。
- 复用仍适用于当前源码和环境的真实 PASS 证据；不得把模拟视口当真实设备、静态审查当实际测试。

### B. Deployment Gate（进入 Work 前）
必须一次性记录：①原 Site URL／Project ID；②本次改动及明确非目标；③目标源码分支和 SHA 或已保存的 Sites 版本；④按风险执行的测试及证据；⑤数据／配置／权限风险；⑥实际可用的 Sites 发布工具；⑦发布后可观察的成功标准。**不满足关键条件则暂停该步骤，不靠重复改 Prompt 假设工具会出现。**

### C. Work 最小交接与发布
- 需求、设计、代码、修复、测试、GitHub 提交与审查均由 Chat 主导；**Work 仅执行用户要求的 ChatGPT Sites 发布及直接相关的发布后验证**。若 Work 发现必须修复代码，返回 Chat，不在 Work 重新开发。
- 目标 Site 已有正确的已保存版本且用户要求发布时，优先评估直接发布；不要为了同步无关文档或追版本号重复修改源码。
- 一次受控发布：确认原 Site 和发布来源 → 执行发布 → 查看平台结果及健康状态 → 在工具允许时检查生产页面受影响功能 → 报告。不得擅自创建新 Site 或改变 URL、数据、配置、权限。
- **禁止版本追逐循环**：Sites 内部分配的版本号或时间不得通过猜测下一版本、硬编码 Footer、再发布来追齐。先核实受支持的运行时／发布元数据读取方式；若无，明确限制，建议应用版本或可靠时间字段，等待用户决定。

### D. 故障停止与证据
- 发布返回 `no healthy upstream` 或其他错误：保存原始错误、版本／Deployment ID、时间和健康检查结果；先诊断影响与平台状态。**无新证据、无明确恢复方案时不得盲目重复发布**；必要时依据已验证回滚方案处理。
- 独立报告 `GitHub Commit/Merge`、`Sites Saved`、`Sites Deployed`、`Production Verified`、`User confirmed`；不得互相推导。旧复盘的未知结果在后续出现新证据时按时间更新，保留历史。
- 生产验证仅针对改动及必要回归；发布成功并达到用户终点后立即 STOP，不追加无关全站测试、文档工作或再次发布。
- 简短收尾：目标、实际改动、源码 SHA／保存版本、Sites 发布结果及版本（未知则写未知）、受影响功能验证、剩余风险／阻塞。完整文档更新按既有状态追踪要求做必要记录，但**不得作为低风险小改动的无关发布前置门槛**。

### E. 五个回归场景（SOP 自检）
1. Footer 一行修改 → 局部验证，不自动全站 UAT。
2. 正确的已保存版本可发布 → 不重复改源码。
3. Sites 版本元数据不可读取 → 不猜版本、不循环部署。
4. 发布失败且无新诊断 → 停止盲目重试，记录阻塞。
5. 发布成功且满足用户终点 → 证据收尾，停止额外操作。


## 2026-11-06 Chat → Work 指令交付规则（最高优先级）

- **MUST：所有开发阶段在当前 Chat 模式完成**：需求、PM/PRD、架构/UI、编码、修复、测试、回归、代码审查及 GitHub 更新均由 Chat 主导，使用 Chat 当前实际可用的工具。不能执行的操作明确标记未完成，不得伪称通过。
- **MUST：需要 Work 时只输出指令，不自动切换模式**。仅在用户明确需要 ChatGPT Sites 部署且满足 Deployment Gate 后，在**当前 Chat 对话直接给出一份完整、可复制的 Work 指令**。禁止以“必须切换到 Work 模式／另开 Work 对话才能继续”作为答复，禁止自动触发 Work handoff、跳转或创建 Work 任务。是否复制并使用该指令，由用户自行决定。
- Work 指令必须包含：原 Site 标识与 URL、仓库及精确 SHA／已保存版本、已完成的 Chat 测试证据、本次仅部署的范围、保护数据／配置／权限的约束、发布后最小验证及失败停止条件。未知字段明确标记待提供；**指令生成 ≠ Work 已执行 ≠ Sites 已部署**。
- 若 Chat 阶段缺少必要开发或测试工具，说明真实阻塞并给出可在 Chat 协作的替代执行步骤；不得将未完成开发或测试偷偷纳入 Work 指令。
- 本规则覆盖文件内所有较早的“交给 Work”“进入 Work”“Work 可负责开发／测试”“切换模式”之类冲突表述；这些词仅可理解为**在 Chat 输出 Work 指令供用户选择使用**，不得自动切换。
