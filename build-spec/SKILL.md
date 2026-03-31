---
name: build-spec
description: Generate or refresh a decision-complete project spec set by interrogating requirements in Planning mode before implementation. Use when the user says `构建文档索引`, `构建spec`, asks to generate PRD/spec/planning documents, or wants an AI to inspect an empty or existing project, ask blocking product and architecture questions, then produce linked docs, initialize the `progress.txt` plus `docs/exec-plans/` memory system, and produce a concise root `AGENTS.md`.
---

# Build Spec

Use this skill to turn a project idea or an existing codebase into a linked, decision-complete documentation system.

## Core Rules

- Detect project state before asking questions.
- If the project is non-empty, inspect it first. Do not ask the user for facts that can be derived from the repo.
- Stay in Planning mode until no high-impact unknowns remain.
- Ask one material question at a time.
- Block on unresolved product, UX, architecture, stack, or data decisions. Do not silently choose defaults for them.
- Write docs only after the questioning loop has removed ambiguity or the user has explicitly confirmed a choice.
- Default document language to English.
- Keep `APP_FLOW.md` in plain English even if the conversation happens in another language.
- Maintain a two-layer memory system: lightweight `progress.txt` plus detailed files under `docs/exec-plans/`.

## Workflow

### 1. Detect Project State

Inspect the target workspace before asking questions.

- Treat the project as empty when there are no meaningful source/config/doc files, only editor metadata, or only ignore files.
- Treat the project as non-empty when there is code, manifests, routes, schemas, generated UI, or existing docs worth analyzing.
- Read [references/inspection-checklist.md](references/inspection-checklist.md) for what to inspect.

### 2. Choose the Path

If the project is empty:
- Ask about the product idea from first principles.
- Continue until product intent, audience, flows, stack, design system, backend model, and rollout sequence are all explicit.
- Initialize `progress.txt` from the confirmed answers once the initial doc set is ready.
- Initialize `docs/exec-plans/active/` and `docs/exec-plans/completed/` for future execution tracking.

If the project is non-empty:
- Summarize the current project shape from inspection.
- Ask about intent, gaps, corrections, target audience, missing features, constraints, and planned direction.
- Continue until the future-state spec is explicit and no high-impact assumption remains.
- Initialize or refresh `progress.txt` from the actual current project state plus the newly confirmed direction.
- Initialize or refresh `docs/exec-plans/active/` and `docs/exec-plans/completed/` so detailed execution state has a durable location.

### 3. Run the Questioning Loop

Keep questioning in Planning mode.

- Prefer questions that remove ambiguity from public behavior, architecture, or data design.
- Use concrete options when the tradeoff is obvious.
- When the user answer creates a new dependency, ask the next question that resolves that dependency.
- Do not stop because the current answer seems "good enough."
- Do not generate documents while the plan still depends on hidden assumptions.

Before moving on, you must be able to state:
- What is being built
- Who it is for
- What is in scope and out of scope
- What every primary flow does
- What stack and major dependencies will be used
- What the design system rules are
- How the backend and data contracts work
- In what order the system will be built

### 4. Generate or Update the Document Set

Work under `docs/`. Create the directory if needed.

Required top-level files:
- `docs/PRD.md`
- `docs/APP_FLOW.md`
- `docs/TECH_STACK.md`
- `docs/DESIGN.md`

Required design directories:
- `docs/design-docs/frontend/`
- `docs/design-docs/backend/`

Required root memory file:
- `progress.txt`

Required execution-memory directories:
- `docs/exec-plans/active/`
- `docs/exec-plans/completed/`

Read [references/doc-set.md](references/doc-set.md) before writing.

When the files already exist:
- Merge carefully instead of rewriting blindly.
- Preserve content that is still valid.
- Rewrite sections that conflict with the newly confirmed decisions.
- Remove contradictions across the set.

The files must cross-reference each other:
- `PRD.md` defines what exists
- `APP_FLOW.md` defines how users experience it
- `TECH_STACK.md` defines what builds it
- `DESIGN.md` defines system shape, frontend/backend boundaries, and points to the detailed design docs
- `docs/design-docs/frontend/` stores detailed frontend design, UI rules, component/system patterns, and interaction design
- `docs/design-docs/backend/` stores detailed backend design, data structures, auth rules, service boundaries, and API contracts
- `progress.txt` records only the current global status, active plan pointers, recent completions, and next actions
- `docs/exec-plans/active/` stores detailed plans for work that is currently being executed
- `docs/exec-plans/completed/` stores archived plans for completed work

### 5. Initialize and Maintain `progress.txt`

Create or update `progress.txt` at the repo root as the lightweight control panel for the AI's external working memory.

Rules:
- Treat it as mandatory, not optional.
- If the project is empty, initialize it from the confirmed planning answers and the generated document set.
- If the project is non-empty, initialize or refresh it from the actual current implementation state, existing docs, and newly confirmed direction.
- Keep it short enough to read at the start of every session without polluting context.
- Do not turn it into a long-running diary or full implementation log.
- Follow the structure in [references/templates/progress-template.txt](references/templates/progress-template.txt).
- Record only:
  - current focus
  - active plan file path
  - in-progress items
  - blockers
  - next actions
  - recent completions
  - key reference files
- Include `Last Updated` and a simple status field such as `planned`, `in_progress`, `blocked`, `validating`, or `completed`.
- Push detailed execution notes into files under `docs/exec-plans/active/` or `docs/exec-plans/completed/`.
- If `progress.txt` conflicts with the active exec-plan, treat the active exec-plan plus actual repo state as the source of truth, then repair `progress.txt`.

### 6. Initialize and Maintain `docs/exec-plans/`

Use `docs/exec-plans/` for detailed execution memory.

Rules:
- Create `docs/exec-plans/active/` for plans that are currently being implemented.
- Create `docs/exec-plans/completed/` for plans that have been finished and archived.
- Use stable filenames such as `YYYY-MM-DD-<topic>.md`.
- Keep exactly one authoritative primary active plan unless the user explicitly wants parallel implementation tracks.
- Each meaningful implementation track should have its own plan file instead of overloading `progress.txt`.
- `progress.txt` must point to the currently authoritative active plan file.
- When a task is complete, move or rewrite its plan into `completed/` and update `progress.txt`.
- Detailed logs, verification notes, breakages, and implementation history belong in the plan file, not in `progress.txt`.
- Follow the structure in [references/templates/active-exec-plan-template.md](references/templates/active-exec-plan-template.md) for active plans.
- Follow the structure in [references/templates/completed-exec-plan-template.md](references/templates/completed-exec-plan-template.md) for completed plans.

### 6.5 Resolve Memory Conflicts

When `progress.txt`, an active exec-plan, and the repo disagree:

- Trust actual repo state over both documents for what is already implemented.
- Trust the active exec-plan over `progress.txt` for the current execution thread.
- Repair `progress.txt` after resolving the conflict.
- If both memory files are stale or contradictory, rebuild the current state from the repo and the most recent verified notes.

### 7. Initialize and Maintain `docs/design-docs/`

Use `docs/design-docs/` for detailed design memory.

Rules:
- Create `docs/design-docs/frontend/` for detailed frontend design documents.
- Create `docs/design-docs/backend/` for detailed backend design documents.
- Use stable filenames such as `<topic>.md` or `<domain>-<topic>.md`.
- Keep `docs/DESIGN.md` as the concise architectural index, not the full body of every design decision.
- Push detailed frontend design into the frontend directory instead of creating a top-level `FRONTEND_GUIDELINES.md`.
- Push detailed backend design into the backend directory instead of creating a top-level `BACKEND_STRUCTURE.md`.
- Do not create a top-level `IMPLEMENTATION_PLAN.md`; execution planning belongs in `docs/exec-plans/`.
- Make `docs/DESIGN.md` explicitly point to the relevant detailed design files.
- Follow the structure in [references/templates/design-template.md](references/templates/design-template.md) for `docs/DESIGN.md`.

### 7.5 Resolve Intent and Documentation Conflicts

When user intent, repo state, and existing docs disagree:

- The latest user-confirmed intent is the source of truth for the desired future state.
- Actual repo state is the source of truth for the current implemented state.
- Existing docs are subordinate to both and must be repaired when they drift.
- Do not silently overwrite the distinction between current state and target state. Make the delta explicit in the generated docs and plans.

### 8. Create or Replace `AGENTS.md`

Generate a concise project map at the repo root.

Rules:
- Keep it around 100 lines.
- Treat it as a table of contents, not a full reference manual.
- Include project purpose, stack summary, naming conventions, component/service patterns, design-token pointers, allowed behaviors, prohibited behaviors, and links to deeper docs.
- Explicitly instruct every new AI session, every new terminal session, and every branch switch to read `progress.txt` first for continuity.
- Explicitly instruct the agent to open the active exec-plan referenced by `progress.txt` before making execution decisions.
- Encode the recovery order explicitly:
  1. Read `progress.txt`
  2. Open the active exec-plan referenced there
  3. Open `docs/DESIGN.md`
  4. Open detailed design docs only if needed
- Explain that `progress.txt` must stay lightweight and that detailed implementation tracking belongs in `docs/exec-plans/`.
- Point to `docs/DESIGN.md` for architecture overview and `docs/design-docs/` for detailed frontend/backend design.
- Point to the generated docs instead of duplicating them.

### 9. Validate the Result

Before finishing:
- Check that every required document exists.
- Check that `progress.txt` exists and reflects the current state.
- Check that `docs/exec-plans/active/` and `docs/exec-plans/completed/` exist.
- Check that `docs/design-docs/frontend/` and `docs/design-docs/backend/` exist.
- Check that no document contradicts another.
- Check that `APP_FLOW.md` is plain English.
- Check that versions in `TECH_STACK.md` are explicit, not implied.
- Check that `AGENTS.md` is concise and points to deeper docs.
- Check that `AGENTS.md` tells future sessions to read `progress.txt` first and then the referenced active exec-plan.
- Check that `docs/DESIGN.md` points to the correct detailed design docs.
- Check that the generated docs use stable IDs for tracing requirements, flows, and execution work.
- Check that conflict resolution rules are respected when memory files, docs, and repo state disagree.
- If the project type is not a web app, confirm the documents were adapted rather than forced into a web-only shape.

## Non-Web Adaptation

The six-file structure stays the same for non-web apps, but the content must adapt.

Examples:
- Desktop app: replace route inventory with windows, panels, menu flows, and local-service interactions.
- Mobile app: replace route inventory with screens, navigation stacks, deep links, device permissions, and offline behavior.
- CLI/tooling project: replace page flow with command flow, prompts, flags, configuration, and output/error paths.

Do not keep web-specific sections that do not apply. Replace them with the equivalent interaction and architecture details for the chosen app type.

## Forward Testing

Forward-test this skill when you revise it substantially.

Use realistic prompts such as:
- "Use $build-spec to interrogate this empty directory and prepare a full spec set for a new budgeting web app."
- "Use $build-spec to inspect this existing repository, question me about missing intent, and refresh the linked spec docs."

Success criteria:
- The agent inspects first when the repo is non-empty.
- The agent keeps asking until the remaining unknowns are low impact.
- The resulting doc set is specific, cross-linked, and decision-complete.
- `progress.txt` is initialized and remains short enough for fast session startup.
- `docs/DESIGN.md` stays concise while `docs/design-docs/` carries the detailed architecture and design.
- `docs/exec-plans/` carries the detailed execution history without bloating `progress.txt`.
- `AGENTS.md` stays concise.

## Traceability IDs

Use lightweight IDs so the documents can reference each other without ambiguity.

- In `PRD.md`, assign feature IDs such as `F-1`, `F-2`.
- In `APP_FLOW.md`, assign flow IDs such as `FLOW-1`, `FLOW-2`.
- In `docs/design-docs/frontend/` and `docs/design-docs/backend/`, reference the relevant feature and flow IDs when describing architecture or contracts.
- In `docs/exec-plans/active/`, reference the feature and flow IDs being implemented.
- Use IDs to connect product requirements, flows, design decisions, and execution work.

## Minimal Examples

Use these examples as shape references when generating a first pass. Do not copy their domain content blindly.

- Empty-project bootstrap example: [references/examples/empty-project-bootstrap.md](references/examples/empty-project-bootstrap.md)
- Existing-project refresh example: [references/examples/existing-project-refresh.md](references/examples/existing-project-refresh.md)
