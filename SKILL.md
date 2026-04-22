---
name: build-harness
description: Build or refresh a minimal AI Harness for an empty or existing project. Supports Claude Code and Codex. Use when the user says `build harness`, `setup harness`, `init harness`, `构建harness`, `构建spec`, `构建文档索引`, or asks to set up AI agent workflows, project rules, verification scripts, sub agent pipelines, or a unified project knowledge layer.
---

# Build Harness

Use this skill to turn an empty project or an existing repository into a small, durable Harness that lets AI work with project context, rules, repeatable workflows, and verifiable outcomes.

A Harness is not just documentation. It is the combination of spec knowledge, behavior rules, verification scripts, and optionally sub agent pipelines that makes AI output stable, correct, and maintainable across sessions.

## Core Rules

- Detect the target AI tool before generating any tool-specific files.
- Inspect the target workspace before asking questions.
- If the project is non-empty, do not ask for facts that can be derived from the repo.
- Keep asking until high-impact product, architecture, verification, or AI-tooling decisions are explicit.
- Default generated document language to the user's language unless the user requests otherwise.
- Prefer the smallest usable Harness over a large documentation tree.
- Preserve valid existing docs. Do not delete or rewrite existing docs blindly.
- Treat Rule, Skill, and Sub Agent as AI-tool-specific assets, not generic `docs/harness/` documents.
- For Claude Code:
  - behavior and recovery instructions belong in root `CLAUDE.md`
  - path-scoped rules belong in `.claude/rules/*.md`
  - project skills belong in `.claude/skills/<skill-name>/`
  - sub agents belong in `.claude/agents/<name>/AGENT.md`
  - permissions belong in `.claude/settings.json`
- For Codex:
  - behavior and recovery instructions belong in root `AGENTS.md`
  - command execution rules belong in `.codex/rules/*.rules`
  - project skills belong in `.agents/skills/<skill-name>/`
  - new skills must follow `$skill-creator` structure
- Maintain a two-layer execution memory system: lightweight `progress.txt` plus detailed files under `docs/exec-plans/`.

## Minimal Harness Outputs

Generate or refresh these by default:

- `CLAUDE.md` (Claude Code) or `AGENTS.md` (Codex)
- `progress.txt`
- `docs/PRD.md`
- `docs/DESIGN.md`
- `docs/dev-map.md`
- `docs/harness.md`
- `docs/exec-plans/active/`
- `docs/exec-plans/completed/`
- `scripts/verify.sh` or `scripts/verify.ps1`

Generate these only when needed:

- `docs/APP_FLOW.md` or `docs/design-docs/app-flow.md` for complex UI/product flows
- `docs/design-docs/*` for complex architecture that needs detailed design files
- `docs/task-board.md` for project-level task portfolio tracking beyond `progress.txt` and exec-plans
- Claude Code: `.claude/rules/*.md` for path-scoped behavior rules; `.claude/settings.json` for permissions
- Codex: `.codex/rules/*.rules` for command approval/sandbox rules
- Claude Code skills: `.claude/skills/<name>/SKILL.md`; Codex skills: `.agents/skills/<name>/SKILL.md`
- Claude Code sub agents: `.claude/agents/<name>/AGENT.md`; Codex sub agents: `.agents/skills/<name>/SKILL.md`

Do not create these as default Harness documents:

- `docs/TECH_STACK.md` — merge into `docs/DESIGN.md`
- `docs/harness/rules.md` — Rule bodies belong in the AI instruction file or tool-specific rules paths
- `docs/harness/skills.md` — Skill bodies belong in their tool-specific skill paths

## Workflow

### 0. Detect AI Tool

Read [references/inspection-checklist.md](references/inspection-checklist.md) for the detection rules.

- `.claude/` present, `.codex/` absent → **Claude Code**
- `.codex/` present, `.claude/` absent → **Codex**
- Both present → ask the user which is the primary tool
- Neither present → ask the user (offer Claude Code and Codex with a one-line description of each)

Record the result as `[TARGET_TOOL]`. Every subsequent step that generates tool-specific files must use this value to choose paths and file formats.

### 1. Detect Project State

Inspect the target workspace before asking questions.

- Treat the project as empty when it contains no meaningful source/config/doc files.
- Treat the project as non-empty when it contains code, manifests, routes/screens/commands, schemas, CI, generated UI, existing docs, `AGENTS.md`, `progress.txt`, `.codex/rules/`, or `.agents/skills/`.
- Read [references/inspection-checklist.md](references/inspection-checklist.md) for what to inspect.

### 2. Choose the Path

If the project is empty:

- Ask from first principles about product goal, audience, primary flows, stack, verification strategy, initial AI rules, and repeatable work that may need skills.
- Generate only the minimal Harness outputs unless the user confirms optional docs are needed.
- If code is not initialized yet, create a verification script that exits with a clear "not configured yet" message and records the needed commands in `docs/harness.md`.

If the project is non-empty:

- Summarize the current project shape from inspection.
- Identify existing docs, duplicate docs, drifted docs, verification scripts, CI commands, Codex rules, and project skills.
- Ask only about desired target state, missing requirements, business rules, verification gaps, or AI-tool preferences that cannot be inferred.
- Preserve useful existing docs. Optional old docs such as `APP_FLOW.md`, `TECH_STACK.md`, `task-board.md`, or `docs/design-docs/*` may remain and be referenced from the new minimal Harness.
- If duplicate or drifted docs exist, plan a gradual merge into `docs/PRD.md`, `docs/DESIGN.md`, `docs/dev-map.md`, or `docs/harness.md`.

### 3. Run the Questioning Loop

Keep questioning until you can state:

- what the project is and who it is for
- what is in scope and out of scope
- what the current repo already contains
- what the target Harness must enable
- how implementation tasks should flow
- how results are verified
- which AI tool is targeted (`[TARGET_TOOL]`)
- which rules are behavior instructions versus command execution policy
- which repeatable procedures require real skills
- whether multi-role sub agent separation is needed

Do not generate docs while the Harness still depends on hidden assumptions.

### 4. Generate or Update the Harness

Read [references/doc-set.md](references/doc-set.md) before writing.

#### Generate the Verification Script

Read [references/verify-by-stack.md](references/verify-by-stack.md) before writing `scripts/verify.*`.

Build the script from three layers in order:

1. **Universal gates** — always include: build, tests pass, test count regression, no secrets, no merge markers, harness doc freshness.
2. **Stack gates** — derive from detected manifests and tooling (e.g. `package.json` → Node.js gates, `.csproj` → .NET gates). Do not invent gates for stacks not present.
3. **Project gates** — derive from evidence found during inspection: forbidden APIs listed in `AGENTS.md` or `.codex/rules/`, localization systems, state-file access policies, file-length limits, sync-required config copies, and similar business rules already encoded in the repo.

If build or test commands cannot yet be inferred, write the script with a clear `echo "Not configured yet — run <command> to initialize"` and `exit 1` for each missing gate. Record the blocker in `docs/harness.md` under Verification.

After generating the script, update the Verification section of `docs/harness.md` to list every gate name and the command it wraps.

Use the default templates when creating new files:

- Claude Code instruction file: [references/templates/claude-md-template.md](references/templates/claude-md-template.md)
- Codex / other AI instruction file: [references/templates/agents-md-template.md](references/templates/agents-md-template.md)
- [references/templates/prd-template.md](references/templates/prd-template.md)
- [references/templates/design-template.md](references/templates/design-template.md)
- [references/templates/dev-map-template.md](references/templates/dev-map-template.md)
- [references/templates/harness-template.md](references/templates/harness-template.md)
- [references/templates/progress-template.txt](references/templates/progress-template.txt)
- [references/templates/active-exec-plan-template.md](references/templates/active-exec-plan-template.md)
- [references/templates/completed-exec-plan-template.md](references/templates/completed-exec-plan-template.md)

Use optional templates only when needed:

- [references/templates/app-flow-template.md](references/templates/app-flow-template.md)
- [references/templates/task-board-template.md](references/templates/task-board-template.md)
- [references/templates/detailed-design-template.md](references/templates/detailed-design-template.md)

### 5. Maintain `progress.txt` and Exec Plans

Create or update `progress.txt` at the repo root as the lightweight control panel.

Rules:

- Keep it short enough for every new session to read first.
- Record only current focus, active plan path, in-progress items, blockers, next actions, recent completions, and key references.
- Detailed implementation notes belong in `docs/exec-plans/active/` or `docs/exec-plans/completed/`.
- If `progress.txt` conflicts with an active exec-plan, trust the active exec-plan for current execution and actual repo state for implemented truth, then repair `progress.txt`.
- Keep exactly one authoritative active plan unless the user explicitly wants parallel tracks.

### 6. Create or Refresh the AI Instruction File

Generate the instruction file appropriate for `[TARGET_TOOL]`:

- **Claude Code** → root `CLAUDE.md`. If an `AGENTS.md` already exists and is valid, import it with `@AGENTS.md` rather than duplicating its content.
- **Codex** → root `AGENTS.md`.

Both must:

- Be treated as the AI entrypoint and table of contents, not a full manual.
- Include project purpose, stack summary, recovery order, required verification, behavior rules, prohibited behaviors, and links to deeper docs.
- Instruct new AI sessions, new terminal sessions, and branch switches to read `progress.txt` first.
- Instruct agents to open the active exec-plan referenced by `progress.txt` before implementation decisions.
- Use this recovery order:
  1. `progress.txt`
  2. active exec-plan referenced there
  3. `docs/dev-map.md`
  4. `docs/harness.md`
  5. `docs/DESIGN.md` and `docs/PRD.md` as needed

Claude Code `CLAUDE.md` must also point to:
- `.claude/rules/*.md` when path-scoped rules exist
- `.claude/skills/*/SKILL.md` when project skills exist
- `.claude/agents/*/AGENT.md` when sub agents exist

Codex `AGENTS.md` must also point to:
- `.codex/rules/*.rules` when command rules exist
- `.agents/skills/*/SKILL.md` when project skills exist

### 7. Handle Rules and Skills

Rules by tool:

| Purpose | Claude Code | Codex |
|---|---|---|
| Behavior instructions | `CLAUDE.md` | `AGENTS.md` |
| Path-scoped / command rules | `.claude/rules/*.md` | `.codex/rules/*.rules` |
| Permissions (enforced) | `.claude/settings.json` | no direct equivalent |

- Record the mapping and reload notes in the Tool Adapters section of `docs/harness.md`.
- Do not store Rule bodies in `docs/harness/rules.md`.

Skills by tool:

| Tool | Path |
|---|---|
| Claude Code | `.claude/skills/<skill-name>/SKILL.md` |
| Codex | `.agents/skills/<skill-name>/SKILL.md` |

- Each skill must contain a `SKILL.md` and may contain `references/`, `scripts/`, or `agents/openai.yaml`.
- When creating or updating a Codex skill, load and follow `$skill-creator`.
- Do not summarize skills as a single `docs/harness/skills.md` file.
- Record the skill index and intended triggers in the Tool Adapters section of `docs/harness.md`.

### 7.5. Set Up Sub Agents (Optional)

Trigger this step when:
- The user explicitly asks for multi-agent role separation, or
- The project has long-running tasks where independent review is critical

Steps:

1. Read [references/sub-agents.md](references/sub-agents.md).
2. Ask the user: compact set (3 agents: Planner, Developer, Verifier) or full set (7 agents)? Default to compact.
3. Generate agent files in the path appropriate for `[TARGET_TOOL]`:
   - Claude Code → `.claude/agents/<name>/AGENT.md`
   - Codex → `.agents/skills/<name>/SKILL.md`
4. Write each agent's role, inputs, outputs, handoff trigger, and return conditions using the templates in `references/sub-agents.md`.
5. Update the Sub Agents section in `docs/harness.md` Tool Adapters with a table of all agents and their triggers.

### 8. Validate the Result

Before finishing:

- Check that the minimal Harness outputs exist.
- Check that optional docs were only created when justified.
- Check that `docs/DESIGN.md` contains tech stack/runtime/CI information instead of requiring a separate `TECH_STACK.md`.
- Check that `docs/harness.md` includes Workflow, Verification, Tool Adapters, and Extension Plan sections.
- Check that the AI instruction file (`CLAUDE.md` or `AGENTS.md`) is concise and points to deeper docs.
- Check that `progress.txt` points to the active exec-plan.
- Check that `scripts/verify.*` exists or the blocker is clearly documented.
- Check that rules, skills, and sub agents (if any) live in the correct tool-specific paths for `[TARGET_TOOL]`.
- If existing optional docs remain, check that they are referenced rather than duplicated.
- If sub agents were created, check that `docs/harness.md` Tool Adapters lists all agents with their triggers.

## Adaptation Rules

### AI Tool Paths

Always use `[TARGET_TOOL]` to select the correct paths:

| Asset | Claude Code | Codex |
|---|---|---|
| Instruction file | `CLAUDE.md` | `AGENTS.md` |
| Behavior rules (path-scoped) | `.claude/rules/*.md` | `.codex/rules/*.rules` |
| Permissions | `.claude/settings.json` | no equivalent |
| Project skills | `.claude/skills/<name>/SKILL.md` | `.agents/skills/<name>/SKILL.md` |
| Sub agents | `.claude/agents/<name>/AGENT.md` | `.agents/skills/<name>/SKILL.md` |

### Documentation Shape

- Web apps may need optional `APP_FLOW.md` or detailed UI docs.
- Desktop apps should describe windows, panels, menus, local services, and packaging instead of routes.
- Mobile apps should describe screens, navigation stacks, permissions, offline behavior, and distribution.
- CLI/tooling projects should describe commands, flags, config files, prompts, outputs, and error paths.
- Libraries should describe public APIs, compatibility, examples, test matrix, and release process.

### Verification Script Shape

Use [references/verify-by-stack.md](references/verify-by-stack.md) for concrete gate lists. Summary per type:

- **Web/Node.js/TS** — lint, typecheck, bundle, no `console.log` in prod, audit, env var completeness.
- **Python** — ruff/flake8, mypy/pyright, pytest, no bare `except`, pinned deps.
- **.NET/C#** — `dotnet build`, `dotnet test`, language version cap, no `MessageBox.Show`, no hardcoded UI strings, XAML encoding, `.csproj` completeness, `dotnet format`.
- **Go** — build, test, vet, golangci-lint, `go mod tidy` diff-free.
- **Rust** — build, test, clippy warnings-as-errors, fmt check, audit.
- **Mobile iOS** — xcodebuild test, swiftlint, no force-unwrap in prod, no hardcoded URLs.
- **Mobile Android** — gradle build/test, lint, no hardcoded strings, no committed secrets.
- **CLI/Tooling** — build, test, `--help` exits 0, shellcheck, no hardcoded absolute paths.
- **Library/SDK** — doc build, example compilation, public API surface audit.

Always add project-specific gates when inspection reveals forbidden patterns, localization systems, shared config sync requirements, or other business rules encoded in `AGENTS.md` or `.codex/rules/`.

## Forward Testing

Forward-test this skill when you revise it substantially.

Use realistic prompts such as:

- "Use $build-harness on this empty project with no .claude/ or .codex/ directory." → should ask which AI tool to target
- "Use $build-harness to inspect this existing repository that has a .claude/ directory." → should auto-detect Claude Code, generate CLAUDE.md
- "Use $build-harness to inspect this existing repository that has a .codex/ directory." → should auto-detect Codex, generate AGENTS.md
- "Use $build-harness to set up sub agents for this project." → should read sub-agents.md, ask compact vs full, generate agent files in tool-correct paths
- "Use $build-harness to add project skills for repeated build and release workflows." → should generate skills in tool-correct paths

Success criteria:

- AI tool is detected before any tool-specific files are generated.
- Empty projects are questioned before docs are generated.
- The default output does not create unnecessary docs.
- Claude Code projects get `CLAUDE.md`, `.claude/rules/`, `.claude/agents/`, `.claude/skills/` as appropriate.
- Codex projects get `AGENTS.md`, `.codex/rules/`, `.agents/skills/` as appropriate.
- Sub agents use the compact set (3) by default; full set (7) only when requested.
- `APP_FLOW.md`, `TECH_STACK.md`, `task-board.md`, and `docs/design-docs/*` remain optional.
- `progress.txt` remains short and `docs/exec-plans/` carries detailed execution memory.

## Minimal Examples

Use these examples as shape references. Do not copy their domain content blindly.

- Empty-project bootstrap example: [references/examples/empty-project-bootstrap.md](references/examples/empty-project-bootstrap.md)
- Existing-project refresh example: [references/examples/existing-project-refresh.md](references/examples/existing-project-refresh.md)
