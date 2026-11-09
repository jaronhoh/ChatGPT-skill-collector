# Agent Instructions — v4.0

This repository contains reusable, opt-in guidance, not executable automation. Existing nine Skill paths remain stable. These rules do not override platform/tool restrictions.

## Scope and priority
1. Follow the user's actual requested endpoint and explicit mode preference; do not infer Sites deployment from GitHub updates.
2. Read only the matched Skill via `SKILL.md`; record path and version actually loaded. File existence or a router link is not proof of loading. If unavailable, disclose it.
3. Preserve existing functionality and project data by default. Classify proposed changes as **Preserve / Change / Remove**; Remove requires explicit user authorization.
4. Do not apply PM/PRD, Ponytail, C2C, architecture, handoff or deployment procedures to ordinary tasks unless explicitly matched.

## GitHub Chat-Only Policy
GitHub reads, edits, commits, PRs, merges, reviews and available CI operations are performed in the current **Chat** with an authorized GitHub Connector. Do not ask, suggest or initiate Work because a task involves GitHub, many files, code, CI, a failed clone or a missing checkout. Diagnose connector errors and use safe authorized alternatives in Chat; if blocked, report the precise missing permission/capability and checkpoint. GitHub-only tasks **never** trigger Sites preflight or Work instructions. Respect any platform-enforced limitation; never fabricate a write.

## Sites boundary
Only an explicit request to deploy/update an actual ChatGPT Site activates the Sites Skill. Development, testing and GitHub remain Chat-first. Separate GitHub HEAD, Sites bound source HEAD, artifact digest, saved Site version, production deployment and user acceptance. If a necessary Sites operation is unavailable in Chat, report the specific remaining gate and verified capability; do not treat a handoff instruction as deployment. Never auto-switch modes or re-run completed work. An explicit user refusal of Work is binding for the current task.

## Verification and completion
Reuse tests only when source, dependencies, configuration and risk scope still match; otherwise test the changed behavior and affected regression paths. Documentation-only changes require structure, route, reference and contradiction checks plus remote readback. Every status must distinguish **Verified / User-reported / Not verified / Blocked**. A GitHub commit is not a Site deployment; Save Version is not Deploy; deployment is not user acceptance. Stop at the user's requested endpoint.

## Sources and Project copies
`SKILL.md` routes to remote Markdown; remote files must be actually read. Project-uploaded `AGENTS.md` and `SKILL.md` are static snapshots and must be refreshed after changes. Do not claim these files install native Skills, connectors or background automation.
