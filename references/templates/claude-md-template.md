# CLAUDE.md

> Use this template for Claude Code projects. For Codex and other general AI tools, use `agents-md-template.md` (AGENTS.md).

Last Updated: <YYYY-MM-DD HH:MM TZ>

## Project

<One-sentence project purpose and stack summary.>

## Recovery Order

Read these files in order at the start of every session, after every branch switch, and after every tool restart:

1. `progress.txt` — current focus, active plan path, blockers, next actions
2. Active exec-plan referenced in `progress.txt`
3. `docs/dev-map.md` — where to find things in the repo
4. `docs/harness.md` — workflow, verification, tool adapters
5. `docs/DESIGN.md` and `docs/PRD.md` — only when architecture or scope is in question

Do not make implementation decisions before reading `progress.txt` and the active exec-plan.

## Verification

Run `scripts/verify.<sh|ps1>` before marking any task complete. Do not self-certify work as done without running it.

## Behavior Rules

- <rule: what to always do>
- <rule: what to never do>

## Prohibited Behaviors

- Do not modify files outside the current task scope without flagging it.
- Do not skip verification under time pressure.
- Do not rewrite existing docs that are still valid.
- <project-specific prohibition>

## Tool Adapters

- Path-scoped rules: `.claude/rules/*.md`
- Project skills: `.claude/skills/*/SKILL.md`
- Sub agents: `.claude/agents/*/AGENT.md`
- Permissions: `.claude/settings.json`

## Key References

- `docs/PRD.md` — product requirements and acceptance criteria
- `docs/DESIGN.md` — architecture, stack, module boundaries
- `docs/dev-map.md` — repo navigation and change paths
- `docs/harness.md` — workflow, verification gates, extension plan
- `docs/exec-plans/active/` — current execution plans
