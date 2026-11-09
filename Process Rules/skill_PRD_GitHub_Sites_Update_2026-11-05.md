---
name: prd-github-sites-update
description: 仅在用户明确要求部署或更新实际 ChatGPT Site 时使用；正常发布唯一权威流程。
version: '4.0'
updated: '2026-11-09'
---

# ChatGPT Sites — Normal Deployment

> **Activation:** explicit Site release/update only. The historical filename is retained for existing links. GitHub-only and PRD-only tasks are outside this Skill. Global boundaries: `../../AGENTS.md`. Fault recovery: [Recovery](skill_Development_Deployment_Recovery_2026-11-08.md), **only on a real failure**.

## 1. Establish target and checkpoint
Identify existing Site ID/URL, access policy, current production version, deployment ID if available, bound Sites source repository/branch/HEAD or supported artifact entry, GitHub repository/branch/target SHA, last known good release, and the first unfinished gate. Do not create a replacement Site, reset data, change secrets, permissions or URL without explicit authorization. If Site already meets the requested outcome, stop.

## 2. Change contract before source sync
Record **Preserve / Change / Remove** for affected functionality and integrations. Preserve is default; deletion needs explicit approval. Compare target GitHub changes against **actual existing Sites source and production behavior**, not just GitHub main. Keep Site-specific working features (including applicable Yahoo Finance, Payment Date, CSV/JSON, calculation logic, storage and owner-only access) unless explicitly removed. Historical V3 and V4 are separate baselines; never infer feature presence from version number alone.

## 3. Provenance and release gate
Track independently:
- `GITHUB_TARGET_SHA`: exact tested GitHub commit;
- `SITES_SOURCE_SHA`: actual Site-bound source commit (may differ from GitHub);
- `ARTIFACT_DIGEST`: if an artifact is built;
- `SITE_VERSION`, `DEPLOYMENT_ID`, `PRODUCTION_VERIFIED`, `USER_ACCEPTED`.

Different SHAs are not automatically a mismatch; prove mapping, content diff and release provenance. Verify authorized sync/build/Save Version/Deploy/readback capabilities **before destructive operations**. Never invent a Sites API, source mapping, deployment tool or permission. Reuse CI/tests only for matching source, dependencies, configuration and risk. Run targeted regressions for preserved integrations and changed functionality.

## 4. Execute only unfinished gates
`SOURCE_VERIFIED → IMPLEMENTED → TESTED → GITHUB_COMMITTED → SITE_SOURCE_SYNCED → BUILT (if applicable) → VERSION_SAVED → DEPLOYED → PRODUCTION_VERIFIED`; `USER_ACCEPTED` is separate. Confirm remote state before retrying an operation with possible side effects. Sync only the approved delta; inspect source after sync; build using verified project commands; save a version; deploy **the existing Site**; verify the real URL, release ID/version, impacted functions, permissions and data. Do not claim success based solely on GitHub CI or a Save Version response.

## 5. Tools and mode boundary
GitHub changes are done in **Chat** using GitHub Connector; no Work prompt for GitHub-only tasks. For an explicitly requested Site deployment, use only tools actually available and authorized. If the remaining Sites gate cannot be executed here, report the checkpoint, verified missing capability and next safe step. A Work instruction is permitted only if user has not rejected Work, its needed capability is verified and platform rules allow; it is **not** a deployment and must not restart development/testing. Never auto-create a Work task. An explicit Chat-only preference must be respected.

## 6. Failure
On a real sync/build/Save/Deploy/verification failure, load [Recovery](skill_Development_Deployment_Recovery_2026-11-08.md). Preserve last known good source, data and checkpoint; resume at first unfinished gate. Do not restart entire pipeline or declare all stages BLOCKED from one failed path.

## 7. Report
Provide actual GitHub SHA, Site source SHA or provenance (or unverified), tests reused/run, preserve/change/remove checks, saved version, deployment ID, production verification, user acceptance, and any exact remaining blocker. Never equate commit, saved version, deployment and acceptance.
