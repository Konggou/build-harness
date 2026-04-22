# Empty Project Harness Bootstrap Example

Use this as a shape reference for a brand-new project with no meaningful existing code.

## Step 0: Tool Detection

No `.claude/` or `.codex/` directory is present in an empty project. Ask the user which AI tool to target before generating any files.

Example question: "Which AI tool will work with this project — Claude Code or Codex (or another tool)?"

## Default Generated Structure

Paths vary by target AI tool:

**Claude Code:**
- `CLAUDE.md`
- `progress.txt`
- `docs/PRD.md`
- `docs/DESIGN.md`
- `docs/dev-map.md`
- `docs/harness.md`
- `docs/exec-plans/active/YYYY-MM-DD-bootstrap-harness.md`
- `docs/exec-plans/completed/`
- `scripts/verify.sh` or `scripts/verify.ps1`

**Codex:**
- `AGENTS.md`
- `progress.txt`
- `docs/PRD.md`
- `docs/DESIGN.md`
- `docs/dev-map.md`
- `docs/harness.md`
- `docs/exec-plans/active/YYYY-MM-DD-bootstrap-harness.md`
- `docs/exec-plans/completed/`
- `scripts/verify.sh` or `scripts/verify.ps1`

## Questioning Focus

Ask until these are explicit:

- which AI tool is targeted (Claude Code or Codex)
- project goal and audience
- initial feature scope
- preferred stack and runtime
- expected verification commands
- first implementation milestone
- whether complex UI flow docs are needed
- whether any repeated workflow should become a project skill
- whether multi-role sub agents are needed (offer compact set of 3 as default)
- whether any command rules or path-scoped rules are needed

## Verification Script

For an empty project where no build/test commands exist yet, generate a placeholder script:

```bash
#!/usr/bin/env bash
echo "Verification not yet configured. Run the following to initialize:"
echo "  <stack-specific init command>"
exit 1
```

Record the blocker in `docs/harness.md` under Verification → Known blockers.

Once the stack is known (from user answers), pre-populate the script with the stack-appropriate gates from `references/verify-by-stack.md` even before code exists, so the AI knows what to run once the project is initialized.

## Example Output Shape

### `CLAUDE.md` (Claude Code) or `AGENTS.md` (Codex)

- Project purpose and stack summary
- Recovery order: progress.txt → active exec-plan → dev-map → harness.md → DESIGN.md
- Verification entrypoint: `scripts/verify.*`
- Behavior rules and prohibited behaviors
- Pointers to deeper docs

### `docs/PRD.md`

- Summary
- Users
- Goals
- Scope with `F-1`, `F-2`
- Acceptance criteria
- Current state: empty project
- Target state: confirmed first milestone

### `docs/DESIGN.md`

- Runtime/framework
- Module boundaries
- Verification commands (or TODO with blocker reason)
- Optional design docs: none by default

### `docs/dev-map.md`

- Start here
- Planned project map
- Common change paths
- Protected areas

### `docs/harness.md`

- Workflow
- Verification (gates list + known blockers)
- Tool Adapters (Claude Code or Codex section, sub agents table if generated)
- Extension Plan

## Optional: Sub Agents

If the user confirms sub agents are needed, generate the compact set (3 agents) by default:

**Claude Code:**
```
.claude/agents/planner/AGENT.md
.claude/agents/developer/AGENT.md
.claude/agents/verifier/AGENT.md
```

**Codex:**
```
.agents/skills/planner/SKILL.md
.agents/skills/developer/SKILL.md
.agents/skills/verifier/SKILL.md
```

Use templates from `references/sub-agents.md`. Record the agent index in `docs/harness.md` Tool Adapters.

## Usage Note

Do not generate optional docs just because templates exist. Add `APP_FLOW.md`, `task-board.md`, or detailed design docs only when the user confirms the complexity is real.
