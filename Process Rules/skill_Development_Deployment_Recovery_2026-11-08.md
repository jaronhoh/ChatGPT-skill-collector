---
name: development-deployment-recovery
description: 仅用于真实开发、GitHub、CI 或 Sites 故障恢复与断点续跑；不取代正常部署。
version: '4.0'
updated: '2026-11-09'
---

# Recovery — Evidence-Based Resume

> Activate on a **real failure** or explicit recovery request. This is not a normal deployment SOP. For normal Sites publishing use [Sites Deployment](skill_PRD_GitHub_Sites_Update_2026-11-05.md). Global Chat-only GitHub policy is in `../../AGENTS.md`.

## 1. Record a bounded failure
Capture task endpoint, stage, exact tool/environment/host, operation, error class, time, permission state, source SHA and last successful checkpoint. Classify **auth / DNS / missing checkout / connector / code / dependency / CI / Sites source / build / save / deploy / verification**. A failed path does not mean the entire task is blocked. Do not guess commands, internal endpoints or unavailable permissions.

## 2. GitHub and network recovery
A missing local checkout does not mean GitHub is inaccessible. Prefer authorized GitHub Connector for reading, writing, PR and CI in **Chat**. Do not ask for Work merely to update GitHub.

DNS circuit breaker key = `(hostname, execution environment, authorization state)`. After a DNS failure, stop identical clone/curl/ls-remote/repeated permission requests **for the same key**. Failure on `github.com` does not prove failure on a separate Sites source host. When network, environment or authorization changes with evidence, allow **one bounded retry** with timeout. Connector errors must be classified separately. If no route is available, report the exact remaining GitHub step, not an invented success.

## 3. Source and deployment recovery
For an existing Site, first inspect last known good production release, bound source and verified historical operation path. Distinguish `GITHUB_SHA`, `SITES_SOURCE_SHA`, artifact digest, saved version and production deployment. Do not assume they share a commit history. Compare the candidate against the Site's current source and **Preserve / Change / Remove** contract. Protect URL, owner-only policy, data, secrets and working integrations. Historical Margin Share Calculator V2, V3 and V4 evidence is version-specific; no past result proves a current deployment tool is available.

Check applicable authorized paths individually: existing saved version, verified Site source sync, supported artifact import/build, official interface, and existing CI. Never invent an API or request unnecessary elevated access. If a step may have completed despite timeout, read actual state before retry.

## 4. Tests and checkpoint
Reuse passing tests only if exact source, dependencies, config and relevant risk match; otherwise run targeted changed-feature and regression tests. Do not require a local Node/Chromium installation when valid cloud CI exists. Distinguish test failure from unavailable test environment.

Checkpoint stages: `SOURCE_VERIFIED → IMPLEMENTED → TESTED → COMMITTED → SOURCE_SYNCED → BUILT → VERSION_SAVED → DEPLOYED → PRODUCTION_VERIFIED`; record `USER_ACCEPTED` separately. Skip non-applicable stages. On resume, re-read remote state and continue from the first incomplete stage. Do not redo successful development or testing without changed evidence.

## 5. BLOCKED and stop conditions
Mark **only the specific unfinished stage** BLOCKED when all applicable authorized routes are ruled out with evidence or indispensable new authorization is missing. Report attempted paths, errors, last good checkpoint, exact missing capability, next safe action and what remains independently achievable. Avoid looping or repeated handoff. Stop when the user's actual requested endpoint is achieved.

## 6. Evidence contract
`GitHub committed ≠ CI passed ≠ Site source synced ≠ Version saved ≠ Production deployed ≠ Production verified ≠ User accepted`. Label each as verified, user-reported, unverified or blocked. Do not turn a suggested Work instruction into a claim of execution. If the user has refused Work, do not ask again.
