# Harness Inspection Checklist

Inspect only what is needed to remove ambiguity before asking the user questions.

## Empty vs Non-Empty

Treat the repo as empty when it contains only:

- editor settings
- ignore files
- empty folders
- placeholder files with no product, architecture, or tooling signal

Treat the repo as non-empty when it contains any meaningful:

- source files
- manifests or lockfiles
- routes, screens, commands, schemas, migrations, or API contracts
- CI or build config
- existing docs
- `AGENTS.md` or other AI instructions
- `progress.txt`
- `docs/exec-plans/`
- `.codex/rules/`
- `.agents/skills/`

## Detect AI Tool First

Before inspecting project content, detect which AI tool the project targets:

- `.claude/` directory present and `.codex/` absent → **Claude Code**
- `.codex/` directory present and `.claude/` absent → **Codex**
- Both present → ask the user which is the primary tool
- Neither present → ask the user (offer Claude Code and Codex as options)

Record the result as `[TARGET_TOOL]` for use in all subsequent steps.

## What to Inspect First

- top-level directories and filenames
- manifests such as `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `pom.xml`, `.csproj`, `.sln`
- build/test config and scripts
- CI files under `.github/`, `.gitlab-ci.yml`, Azure, Jenkins, or similar
- app entrypoints, routes, screens, CLI commands, services, schemas, migrations
- existing `docs/`
- root `CLAUDE.md` (Claude Code) or root `AGENTS.md` (Codex)
- root `progress.txt`
- `docs/PRD.md`
- `docs/DESIGN.md`
- `docs/dev-map.md`
- `docs/harness.md`
- optional legacy docs such as `docs/APP_FLOW.md`, `docs/TECH_STACK.md`, `docs/task-board.md`, and `docs/design-docs/*`
- `docs/exec-plans/active/` and `docs/exec-plans/completed/`
- Claude Code paths: `.claude/rules/*.md`, `.claude/agents/*/AGENT.md`, `.claude/skills/*/SKILL.md`, `.claude/settings.json`
- Codex paths: `.codex/rules/*.rules`, `.agents/skills/*/SKILL.md`

## What to Extract

- current platform, framework, language, and dependency manager
- current feature or API surface
- architecture layers and module boundaries
- current verification commands: lint, typecheck, test, build, package
- existing CI behavior
- target AI tool: Claude Code, Codex, both, or unknown
- current AI instruction file (`CLAUDE.md` or `AGENTS.md`) and whether it is concise or overloaded
- existing rules (`.claude/rules/` or `.codex/rules/`) and their scope
- existing project skills (`.claude/skills/` or `.agents/skills/`) and whether they follow skill structure
- existing sub agents (`.claude/agents/`) and their role assignments
- obvious gaps between implementation and docs
- duplicate docs that should be merged or referenced
- current delivery state from `progress.txt` and exec-plans
- conflicts between repo state, docs, progress, exec-plans, instruction file, rules, skills, and agents

## Questioning Guidance

Do not ask the user:

- which framework is already present when manifests answer it
- which tests exist when commands and test folders answer it
- whether docs already exist when files answer it
- which AI tool is in use when `.claude/` or `.codex/` directories answer it
- whether rules or skills already exist when the paths answer it

Do ask the user:

- intended product outcome or target state
- audience and success criteria
- business rules not encoded in the repo
- verification expectations when commands cannot be inferred
- which optional docs are worth keeping
- which AI tool is primary when both `.claude/` and `.codex/` exist, or when neither exists
- whether repeated workflows should become real skills
- whether multi-agent role separation (sub agents) is needed

## Conflict Resolution

- Actual repo state is truth for what exists now.
- Latest user-confirmed intent is truth for desired future state.
- Existing docs and memory files are fallible summaries.
- Existing optional docs should be preserved when still useful, then referenced from the minimal Harness.
