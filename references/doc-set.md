# Minimal Harness Document Contract

Use this contract to keep the generated Harness small, specific, and mutually consistent.

## Default Outputs

### `CLAUDE.md` (Claude Code) or `AGENTS.md` (Codex)

Generate the file appropriate for the detected AI tool:
- Claude Code → `CLAUDE.md` at the project root. Can import an existing `AGENTS.md` with `@AGENTS.md`.
- Codex → `AGENTS.md` at the project root.

Both must define:

- project purpose and stack summary
- required recovery order
- required verification entrypoint
- basic behavior rules and prohibited behaviors
- pointers to `progress.txt`, `docs/dev-map.md`, `docs/harness.md`, `docs/DESIGN.md`, and `docs/PRD.md`

Claude Code `CLAUDE.md` must also point to:
- `.claude/rules/*.md` for path-scoped rules when they exist
- `.claude/skills/*/SKILL.md` for project skills when they exist
- `.claude/agents/*/AGENT.md` for sub agents when they exist

Codex `AGENTS.md` must also point to:
- `.codex/rules/*.rules` for command rules when they exist
- `.agents/skills/*/SKILL.md` for project skills when they exist

Do not duplicate full architecture, full workflow, or full skill bodies here.

### `progress.txt`

Must live at the project root.

Must define:

- project name
- last updated timestamp
- status
- current focus
- current active plan path
- in-progress items
- blockers
- next actions
- recent completions
- key references

Keep it concise. Detailed history belongs in `docs/exec-plans/`.

### `docs/PRD.md`

Must define:

- project summary
- target users or consumers
- problem and goals
- in-scope features
- explicit out-of-scope items
- acceptance criteria

Use lightweight feature IDs such as `F-1` when traceability helps. For existing projects, distinguish current state from target state.

### `docs/DESIGN.md`

Must define:

- architecture summary
- runtime, framework, language, and dependency choices
- build/test/lint/CI commands when known
- main modules and boundaries
- data, API, storage, integration, packaging, or distribution boundaries as relevant
- optional detailed design docs, if any

This file replaces a default `TECH_STACK.md`. Do not split technology choices into a separate file unless the project already has one or complexity justifies it.

### `docs/dev-map.md`

Must define:

- where to start reading the project
- main directories and responsibilities
- common change paths
- risky or protected areas
- existing patterns to follow
- docs and skills relevant to common work

Do not repeat full architecture prose from `docs/DESIGN.md`; make it an executable navigation map.

### `docs/harness.md`

Must define four sections:

- `Workflow`: task phases, inputs, outputs, handoff rules, and rollback/stop conditions
- `Verification`: unified verification entrypoint, underlying commands, expected outputs, and known blockers
- `Tool Adapters`: Codex `AGENTS.md`, `.codex/rules/*.rules`, `.agents/skills/*`, plus any other AI-tool mappings
- `Extension Plan`: prioritized path from MVP Harness toward stronger scripts, skills, review agents, CI, dev-map maintenance, MCP, and delivery closure

Do not store Rule bodies or Skill bodies here. Store mappings and rationale only.

### `docs/exec-plans/active/`

Must contain detailed execution plans that are currently active.

Each active plan should capture:

- task goal
- scope boundaries
- implementation steps
- verification notes
- known blockers
- immediate next actions

`progress.txt` must point to the current authoritative active plan.

### `docs/exec-plans/completed/`

Must contain archived execution plans for completed work.

Use it for:

- implementation history
- verification context
- remaining risks and follow-ups

### `scripts/verify.sh` or `scripts/verify.ps1`

Must be the unified verification entrypoint.

Rules:

- Existing projects: wrap discovered lint/typecheck/test/build commands where possible.
- Empty projects: create a safe placeholder only when no executable project exists yet.
- If verification cannot be determined, fail with a clear message and document the blocker in `docs/harness.md`.
- Compose the script from three layers: universal gates (build, tests, test-count regression, no secrets, no merge markers), stack-specific gates (derived from detected manifests and tooling), and project-specific gates (derived from business rules, forbidden patterns, and conventions found during inspection). See [verify-by-stack.md](verify-by-stack.md) for concrete gate lists.
- After generating the script, record every gate name and its command in the Verification section of `docs/harness.md`.

## Optional Outputs

### `docs/APP_FLOW.md` or `docs/design-docs/app-flow.md`

Create only for complex UI/product flows.

Must define screens/routes/commands, entry points, decisions, success outcomes, and failure outcomes.

### `docs/design-docs/*`

Create only when detailed architecture cannot fit safely in `docs/DESIGN.md`.

Use domain-specific files, not mandatory `frontend/` and `backend/` directories for every project.

### `docs/task-board.md`

Create only when the project needs a portfolio-level task board beyond `progress.txt` and exec-plans.

### Rules

- Claude Code: `.claude/rules/*.md` — create for path-scoped behavior rules or command guidance
- Codex: `.codex/rules/*.rules` — create for command execution policy (prefixes, approvals, sandbox)

Create only when behavior that belongs in the instruction file needs to be path-scoped or enforced as policy.

### Project Skills

- Claude Code: `.claude/skills/<skill-name>/SKILL.md`
- Codex: `.agents/skills/<skill-name>/SKILL.md`

Create only for real reusable procedures. Follow the skill-creator structure.

### Sub Agents

- Claude Code: `.claude/agents/<name>/AGENT.md`
- Codex: `.agents/skills/<name>/SKILL.md` (used as agent definition)

Create only when the user needs multi-role task separation. Read [references/sub-agents.md](sub-agents.md) before generating. Offer the compact set (3 agents) first; upgrade to full set (7 agents) when the project requires independent review gates.

## Consistency Rules

- `CLAUDE.md` (Claude Code) or `AGENTS.md` (Codex/other) must point to the minimal Harness docs instead of duplicating them.
- `docs/DESIGN.md` must include tech stack/runtime/CI facts unless an existing optional doc is deliberately retained.
- `docs/dev-map.md` must match actual repo paths.
- `docs/harness.md` must not claim Rule/Skill bodies live under `docs/harness/`.
- `progress.txt` must reflect the active exec-plan and actual repo state.
- Existing optional docs may remain, but the Harness should reference them and avoid duplicating their content.
- Current-state descriptions must match the repo; target-state descriptions must match latest user-confirmed intent.
