# Agent Instructions

This repository is a collection of reusable guidance files, not an application runtime. The eight existing `skill_*.md` paths are stable entry points for users who have already imported them.

## Scope and loading

- Start from the user's actual task. Read only the relevant file in `SKILL.md` and any linked task-specific sections; do not read every Skill, architecture guide, database guide, or deployment guide by default.
- Skills are opt-in workflows, not global constraints. A generic coding request does not activate PM Make, PRD Make, Ponytail, C2C, Project Handoff, or deployment guidance.
- User instructions and the target project's verified constraints take precedence over these reusable defaults. Keep framework-specific commands out of framework-agnostic work.
- Do not assume this repository's instructions automatically apply to other GitHub repositories or to ChatGPT Project settings; they must be installed or explicitly referenced.

## Execution and decisions

- **Chat-first**: requirements, design, implementation, fixes, relevant tests, review and GitHub updates are handled in the current Chat conversation with available authorized tools. Use Codex as a separate execution tool only when applicable, not as an excuse to stop work.
- **Work handoff**: when the user requests a ChatGPT Sites deployment, provide a complete, copyable Work-only deployment instruction **in the current Chat**. Do not automatically switch modes or create a Work conversation. Work does not implement or pretest features. Generating instructions is not deployment.
- Proceed independently on authorized, reversible local edits, disposable local test data, read-only inspection, routine test runs, and fixes within the approved scope. Do not pause for permission after every safe step.
- Ask before materially changing product scope, exposing secrets, accessing or modifying production data, destructive/irreversible operations, merging into protected branches, or publishing/deploying to production when not explicitly authorized. Never bypass permissions or fabricate tool capabilities.
- Prefer the existing repository structure, conventions and compatible components. For a new React UI, consider shadcn/ui where suitable; do not force it on other stacks.

## Done means done

For implementation tasks: implement the requested behavior → run the relevant code or tests where tools allow → inspect actual results → fix failures introduced by the change → rerun affected verification. Continue until acceptance criteria are met or a concrete external blocker remains. No mandatory full-suite reruns for a small reversible edit; use risk-proportionate checks. Report unexecuted checks as unverified, not passed.

For documentation-only changes: validate file syntax, paths, links, scope, and conflicting instructions; confirm GitHub writes by reading the committed files. Do not invent runtime tests for a Markdown-only repository.

For deployment tasks: distinguish GitHub committed, CI verified, Sites deployed and production verified. Preserve existing Site URL, data, configuration, secrets and permissions. Stop when the user's requested outcome is met; do not add unrelated release gates.

## Task-specific references

See [SKILL.md](SKILL.md) for exact activation criteria. Read source documents and linked references only when the selected task needs them. Do not expand references recursively without a task-related reason.
