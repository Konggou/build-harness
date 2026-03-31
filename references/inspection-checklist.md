# Project Inspection Checklist

Inspect only what is needed to remove ambiguity before asking the user questions.

## Empty vs Non-Empty

Treat the repo as empty when it contains only:
- editor settings
- ignore files
- empty folders
- placeholder files with no real product or architecture signal

Treat the repo as non-empty when it contains any meaningful:
- source files
- manifests or lockfiles
- routes, screens, pages, or command entrypoints
- schemas, migrations, or API contracts
- design-system files
- existing docs, `AGENTS.md`, or `progress.txt`

## What to Inspect First

- top-level directories and filenames
- app manifests such as `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `pom.xml`
- framework config such as Next/Vite/Nuxt/Tauri/Electron/mobile configs
- route or screen definitions
- API handlers, service modules, schema files, migrations
- existing `docs/` and root `AGENTS.md`
- root `progress.txt`
- `docs/DESIGN.md`
- `docs/design-docs/frontend/` and `docs/design-docs/backend/`
- `docs/exec-plans/active/` and `docs/exec-plans/completed/`

## What to Extract

- current platform and framework
- current feature surface
- architecture layers and module boundaries
- data model shape
- auth/storage/integration hints
- obvious gaps between implementation and existing docs
- current delivery state from `progress.txt`, if present
- detailed ongoing or recently completed execution state from `docs/exec-plans/`, if present
- any conflict between repo state, docs, `progress.txt`, and active exec-plans

## Questioning Guidance

Do not ask the user:
- which framework is already present when manifests answer it
- which pages exist when routes/screens answer it
- whether docs already exist when files answer it

Do ask the user:
- intended product outcome
- audience and success criteria
- desired future-state features
- business rules not encoded in the repo
- target stack changes
- UX decisions that cannot be inferred

## Conflict Resolution

- Use repo state as truth for what is currently implemented.
- Use the latest user-confirmed answer as truth for the intended future state.
- Treat existing docs and memory files as fallible summaries that may need repair.
