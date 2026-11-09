# Agent Instructions

This repository is a collection of reusable guidance files, not an application runtime. Existing `skill_*.md` paths remain stable entry points for users who have already imported them.

## Scope and loading

- Start from the user's actual task. Use the Project-uploaded `SKILL.md` router to read only the matching GitHub-hosted Skill and its task-specific sections; do not read every Skill, architecture guide, database guide, or deployment guide by default.
- Skills are opt-in workflows, not global constraints. A generic coding request does not activate PM Make, PRD Make, Ponytail, C2C, Project Handoff, or deployment guidance.
- User instructions and the target project's verified constraints take precedence over these reusable defaults. Keep framework-specific commands out of framework-agnostic work.
- Do not assume this repository's instructions automatically apply to other GitHub repositories or to ChatGPT Project settings; they must be installed or explicitly referenced.

## Execution and decisions

- **Chat-first**: requirements, design, implementation, fixes, relevant tests, review and GitHub updates are handled in the current Chat conversation with available authorized tools. Use Codex as a separate execution tool only when applicable, not as an excuse to stop work.
- **Sites preflight and Work handoff**: before implementing a change whose requested endpoint includes Sites, verify the release path and current tool capabilities using the matched Sites Skill. For an already implemented change, start at the first unfinished release gate. when the user's stated delivery goal includes ChatGPT Sites deployment (even when they only say 'fix this and ship' or 'continue to production'), if Chat cannot finish the release and Work has a verified capability for the remaining steps, **proactively** provide a complete, copyable Work-only deployment instruction **in the current Chat immediately after Chat-side development, relevant tests and GitHub work are ready**. Do not wait for the user to ask for the instruction a second time. Do not automatically switch modes or create a Work conversation. Work owns authorized release recovery once started, reuses valid development evidence, and never sends a deployment failure back to Chat for repeated development or testing. Generating instructions is not deployment. If Chat-side work is blocked, provide a factual recovery checkpoint and a Work instruction only for remaining operations Work can actually perform; never fabricate a passing SHA or tests.
- Proceed independently on authorized, reversible local edits, disposable local test data, read-only inspection, routine test runs, and fixes within the approved scope. Do not pause for permission after every safe step.
- Ask before materially changing product scope, exposing secrets, accessing or modifying production data, destructive/irreversible operations, merging into protected branches, or publishing/deploying to production when not explicitly authorized. Never bypass permissions or fabricate tool capabilities.
- Prefer the existing repository structure, conventions and compatible components. For a new React UI, consider shadcn/ui where suitable; do not force it on other stacks.

## Done means done

For implementation tasks: on a tool failure, first diagnose and attempt safe, bounded recovery; preserve the last verified SHA and stage rather than restarting completed work. When the user explicitly requests an automatic recovery workflow, use the remote Development & Deployment Recovery URL in the Project's `SKILL.md` router; only claim it was loaded after a successful remote read. Implement the requested behavior → run the relevant code or tests where tools allow → inspect actual results → fix failures introduced by the change → rerun affected verification. Continue until acceptance criteria are met or a concrete external blocker remains. No mandatory full-suite reruns for a small reversible edit; use risk-proportionate checks. Report unexecuted checks as unverified, not passed.

For documentation-only changes: validate file syntax, paths, links, scope, and conflicting instructions; confirm GitHub writes by reading the committed files. Do not invent runtime tests for a Markdown-only repository.

For deployment tasks: reuse verified previous successful deployment paths and valid tests; choose available cloud tools before asking the user to install local dependencies. Diagnose bounded failures without repeating unchanged unsuccessful actions. Distinguish GitHub committed, CI verified, Sites deployed and production verified. Preserve existing Site URL, data, configuration, secrets and permissions. Stop when the user's requested outcome is met; do not add unrelated release gates. A failed deployment requires evidence-based diagnosis and checkpoint preservation; never assume commit equals production. Continue through applicable authorized alternatives after an individual route fails. Mark only the remaining stage BLOCKED once all applicable routes have been ruled out with evidence or an indispensable user authorization is missing.

## Task-specific references

The Project-uploaded `SKILL.md` contains the GitHub raw URLs and exact activation criteria. Fetch only a matched Skill using available authorized tools; record the actual GitHub commit SHA for reproducible work when needed. If the remote read fails, say the Skill was not loaded and continue only with verified local instructions. Uploading `AGENTS.md` and `SKILL.md` does not itself grant network/GitHub access or register native Skills. These Project copies do not automatically update when GitHub changes; re-upload them after router/global-rule edits. Read further references only when relevant.
