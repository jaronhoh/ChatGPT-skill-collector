# Skill — Project Progress, Release Tracking & Cross-Conversation Handoff

**Skill ID:** `project-handoff`  
**Version:** 1.0  
**Created:** 2026-10-16  
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