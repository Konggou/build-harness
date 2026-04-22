# Harness

Last Updated: <YYYY-MM-DD HH:MM TZ>

## Workflow

### Understand

- Inputs: `progress.txt`, active exec-plan, `docs/dev-map.md`, relevant source/docs.
- Output: smallest implementation plan and risk notes.
- Stop or ask when product intent, architecture, or verification is unclear.

### Implement

- Modify only related files.
- Follow existing patterns from `docs/dev-map.md`.
- Create or update tests when behavior changes.
- Use project skills from `.agents/skills/` for repeatable workflows.

### Verify

- Run the unified verification entrypoint.
- Fix failures caused by the change.
- Report unrelated failures separately with evidence.

### Review and Record

- Review the diff for scope, regressions, missing tests, and Harness drift.
- Update `progress.txt` and exec-plans after meaningful progress.

## Verification

- Unified entrypoint: `scripts/verify.<sh|ps1>`

### Gates

| Gate | Command | Layer |
|---|---|---|
| build | `<build command>` | universal |
| tests pass | `<test command>` | universal |
| test count regression | `<baseline check>` | universal |
| no secrets | `<scan command>` | universal |
| <stack-specific gate> | `<command>` | stack |
| <project-specific gate> | `<command>` | project |

### Known Blockers

- <blocker or None>

### Success Criteria

- All gates exit 0.
- <additional observable outcome>

## Tool Adapters

### Claude Code

- Behavior instructions: `CLAUDE.md`
- Path-scoped rules: `.claude/rules/*.md`
- Project skills: `.claude/skills/<skill-name>/SKILL.md`
- Sub agents: `.claude/agents/<name>/AGENT.md`
- Permissions: `.claude/settings.json`
- Reload: restart Claude Code session or reload project

### Codex

- Behavior instructions: `AGENTS.md`
- Command execution rules: `.codex/rules/*.rules`
- Project skills: `.agents/skills/<skill-name>/SKILL.md`
- Sub agents: `.agents/skills/<name>/SKILL.md` (used as agent definition)
- Skill creation: follow `$skill-creator`
- Reload: <restart/reload guidance if needed>

### Sub Agents

Set: compact (3) / full (7) / none

| Agent | File | Trigger |
|---|---|---|
| <agent name> | `<path>` | <when this agent is invoked> |

### Other AI Tools

- <tool>: <path and status, or "Not configured">

## Extension Plan

1. Script rules that can be checked automatically.
2. Promote repeated workflows into `.agents/skills/*`.
3. Add a review pass for non-trivial changes.
4. Wire `scripts/verify.*` into CI.
5. Add dev-map freshness checks.
6. Add MCP or external tool adapters only after local development verification is stable.
