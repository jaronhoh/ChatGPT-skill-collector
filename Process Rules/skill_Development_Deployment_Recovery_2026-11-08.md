---
name: development-deployment-recovery
description: 开发、GitHub、CI、测试和网络故障的恢复及断点续跑；不包含 Sites 部署。
version: '4.1'
updated: '2026-11-10'
---

# Development Recovery — Evidence-Based Resume

> Historical filename is retained to avoid breaking existing references. This Skill does **not** manage ChatGPT Sites publishing or production deployment. Global Chat-only GitHub policy: `../../AGENTS.md`.

## 1. Activate only for a real failure
Record task endpoint, failed stage, actual tool/environment/hostname, operation, error class, authorization state, exact GitHub SHA, last successful checkpoint and next safe action. Classify **authentication / DNS / missing checkout / connector / code / dependency / CI / test environment**. A failed tool route does not mean all routes are blocked.

## 2. GitHub and network recovery in Chat
A missing checkout is not a GitHub outage. Prefer authorized GitHub Connector for reading, editing, commits, PRs and CI. Do not request Work for GitHub tasks. On connector errors inspect response class, branch, permissions and file SHA; reread latest file before safe conflict resolution. Never force overwrite unknown changes.

DNS circuit breaker key: `(hostname, execution environment, authorization state)`. After resolution failure, do not repeat identical clone/curl/ls-remote or permission requests for the same key. If network environment or authorization verifiably changes, permit one bounded retry with timeout. An unrelated host is not automatically blocked.

## 3. Testing and CI
Compare exact source SHA, dependency lockfiles, test configuration and affected risk. Reuse passing evidence only when applicable; otherwise run the smallest sufficient affected tests and regression checks. Prefer existing GitHub Actions and available authorized cloud test artifacts; absence of a local Node or browser installation alone is not proof tests cannot run. Do not invent green CI results.

## 4. Resume checkpoint
Use only relevant stages: `SOURCE_VERIFIED → IMPLEMENTED → TESTED → COMMITTED → CI_VERIFIED → DONE`. Skip stages not required by the task. On resume reread real GitHub state and continue from the first unfinished stage. Do not redo successful development or tests without new evidence.

## 5. Blocked and completion
Mark only the unfinished stage BLOCKED after applicable authorized routes are excluded or indispensable authorization is missing. Report attempted paths, errors, last good checkpoint, precise missing capability and next safe action. No repeated identical retries or Work handoff for GitHub tasks. Stop at the user's requested endpoint.

## 6. Evidence
Distinguish GitHub committed, tests run, CI verified and user acceptance. Label each **Verified / User-reported / Not verified / Blocked**. A proposed action is not proof of execution.
