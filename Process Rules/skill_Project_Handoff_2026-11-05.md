---
name: project-handoff
description: 用户明确要求查询、更新项目状态或跨对话交接时使用。
version: '3.0'
updated: '2026-11-25'
source_type: unknown
original_author: unknown
source_url: 'https://github.com/jaronhoh/ChatGPT-skill-collector/blob/main/Process%20Rules/skill_Project_Handoff_2026-11-05.md'
source_status: local_history_only
source_audit_date: '2026-11-25'
first_known_date: '2026-10-16'
first_known_date_status: historical_record_reported_not_independently_verified
historical_filename: 'skill_Project_Handoff_2026-10-16.md'
verified_history_commit: '799fff73ec9fb3f73ca0e539b3cce0458e82a1cd'
upstream_version: null
upstream_commit: null
license: null
---

# Project Status & Handoff

## 1. Scope
Only activate for explicit project progress queries, status updates or cross-conversation project handoff. Ordinary development, tests, commits and PRs do not trigger this Skill. Follow root AGENTS.md. This Skill records facts; it does not initiate deployment or prescribe a development workflow.

## 2. Read and verify
- Identify project, repository, branch and requested endpoint.
- Read existing canonical status record (normally docs/PROJECT_STATUS.md); reuse equivalent files.
- Check relevant live commits, PRs, CI and other available evidence. Distinguish Verified, User-reported, Not verified and Blocked. A merge does not prove a release or acceptance.
- Identify discrepancies, last completed milestone, remaining work and next action. Do not repeat completed work without new evidence.

## 3. Update only when requested
When the user requests a status update or explicitly establishes a status-maintenance requirement, update the existing canonical record for material changes. Read latest file and SHA before writing, preserve unrelated history, commit via authorized GitHub Connector and verify readback. Do not create status files for read-only queries. If a requested write fails, report the blocker. Preserve existing features, data and permissions.

## 4. Minimal status record
If a new status record is requested and no equivalent exists, use:

```md
# Project Status
- Repository / branch:
- Last updated (timezone):
- User goal / endpoint:

| Area | Status | Evidence |
|---|---|---|
| Requirements | | |
| Implementation | | |
| Tests / CI | | |
| Acceptance | | |

## Remaining work and blockers
- Item / owner / next action / evidence:

## Handoff
- Confirmed decisions and constraints:
- Relevant files and exact commits:
- Last completed checkpoint:
- Next action:

## Change log
| Date | Change | Result | Evidence |
|---|---|---|---|
```

Add release information only if relevant and supported by evidence. Preserve historical entries; never store secrets.

## 5. Completion
Report current state, evidence, unresolved blockers and next action. For requested writes include the canonical file link and verified commit SHA. Stop at the requested endpoint. No automatic Work handoff or deployment.

## 6. Provenance
External author and original upstream remain unconfirmed. Historical records previously identified a 2026-10-16 filename; the earliest independently verified commit for the current filename is 799fff73ec9fb3f73ca0e539b3cce0458e82a1cd (2026-11-05). The source URL is the local maintenance location, not proof of original authorship.
