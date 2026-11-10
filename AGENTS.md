# Agent Instructions — v4.1

This repository contains reusable opt-in guidance, not executable automation. Follow the user's actual task, tool permissions and explicit mode preference. Project-uploaded copies do not update automatically.

## Scope and loading
- Read only the relevant remote Skill selected by `SKILL.md`, and record its actual path/version. A link or file existing in GitHub is not proof of loading.
- Ordinary GitHub operations, code changes and tests do not activate PM, PRD, C2C, Ponytail, architecture or handoff workflows automatically.
- Existing features, data, permissions and project conventions are preserved by default. Use **Preserve / Change / Remove**; Remove needs user authorization.

## GitHub Chat-Only Policy
- GitHub reads, edits, commits, reviews, PRs, merges and available CI operations are performed in the current Chat using authorized connectors.
- Never ask, suggest or initiate Work merely because the request involves GitHub, multiple files, coding, CI, clone failure or missing checkout.
- Diagnose connector errors, verify permissions and attempt safe authorized alternatives in Chat; otherwise report the precise blocker. Do not invent a successful write.
- Do not infer deployment from a GitHub request. **This Skill collection does not define or activate ChatGPT Sites deployment workflows.**

## Verification and completion
- Reuse test evidence only when source, dependencies, configuration and risk still match. For documentation-only changes, verify syntax, links, route references and GitHub readback.
- Distinguish **Verified / User-reported / Not verified / Blocked**. GitHub commit, CI success and production release are separate outcomes.
- Stop at the user's requested endpoint; no unrelated release steps or automatic handoffs.

## Project copies
`AGENTS.md` and `SKILL.md` uploaded to a ChatGPT Project are static snapshots. Refresh them after GitHub updates. These documents do not install native Skills or connectors.
