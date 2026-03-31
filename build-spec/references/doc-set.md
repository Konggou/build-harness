# Document Set Contract

Use this contract to keep the generated files specific, complete, and mutually consistent.

## `PRD.md`

Must define:
- product summary
- target users and audience segments
- problems solved
- goals and success metrics
- in-scope features
- explicit out-of-scope items
- non-goals
- user stories
- feature-by-feature acceptance criteria

Assign feature IDs such as `F-1`, `F-2` so other docs can reference them.

## `APP_FLOW.md`

Write in plain English.

Must define:
- screen or route inventory
- entry points into each flow
- step-by-step user navigation
- decision points
- success outcomes
- failure outcomes
- state transitions and edge cases relevant to UX

Assign flow IDs such as `FLOW-1`, `FLOW-2`.

Adapt for non-web apps by replacing routes/pages with the appropriate interaction unit.

## `TECH_STACK.md`

Must define:
- exact runtime/framework choices
- exact package and dependency versions
- APIs, SDKs, tools, infra, hosting, storage, and CI choices
- integration boundaries

Do not leave version choices vague.

## `DESIGN.md`

Must define:
- architecture summary
- frontend/backend boundaries
- core modules and service relationships
- major data and control flow
- references to the detailed frontend and backend design docs

Keep this file concise. It is the entry point into the deeper design documentation.
Follow `references/templates/design-template.md` as the default structure.

## `docs/design-docs/frontend/`

Must contain detailed frontend design documents, such as:
- UI architecture
- routing and navigation
- state management
- component patterns
- design system or visual rules

For UI-light or non-web projects, adapt the contents to the relevant interaction surface.

## `docs/design-docs/backend/`

Must contain detailed backend design documents, such as:
- service architecture
- data model
- auth and permissions
- API contracts
- jobs, storage, or integrations

If the chosen stack relies on SQL schema design, include exact SQL structures in the relevant backend design document instead of leaving them implied.

## `progress.txt`

Must live at the project root.

Must define:
- current focus
- current active plan path under `docs/exec-plans/active/`
- what is currently in progress
- what is next
- what is blocked, broken, or uncertain
- recent completions
- key references needed to resume work

Initialization rules:
- Empty project: initialize from the confirmed planning answers and the newly generated spec set.
- Existing project: initialize from actual repository state, existing docs, and the newly confirmed direction.

Update rules:
- Update after each meaningful completed feature or milestone.
- Keep it concise enough for every new session to read first.
- Do not use it as a long execution diary.
- Prefer a stable, dashboard-like structure over accumulating narrative notes.
- If it conflicts with the active exec-plan, repair it to match the active exec-plan and actual repo state.

## `docs/exec-plans/active/`

Must contain detailed execution plans that are currently active.

Each active plan should capture:
- task goal
- scope boundaries
- implementation steps
- verification notes
- known breakages or blockers
- immediate next actions
- related feature and flow IDs

`progress.txt` should point to the current authoritative active plan.
If `progress.txt` disagrees with the active exec-plan, treat the active exec-plan as authoritative for the current execution thread.
Use a stable filename such as `YYYY-MM-DD-<topic>.md`.

## `docs/exec-plans/completed/`

Must contain archived execution plans for work that has been completed.

Use it for:
- preserving detailed implementation history
- retaining verification context
- avoiding context bloat in `progress.txt`

Use a stable filename such as `YYYY-MM-DD-<topic>.md`.

## `AGENTS.md`

Must define:
- `progress.txt` as required external memory
- a rule that every new AI session, every new terminal window, and every branch switch reads `progress.txt` first
- a rule that the agent opens the active exec-plan referenced by `progress.txt` before implementation work
- a rule that `progress.txt` is updated faithfully after each meaningful implementation step
- a rule that detailed execution tracking belongs in `docs/exec-plans/`, not in `progress.txt`
- a rule that `docs/DESIGN.md` is the design index and detailed design lives in `docs/design-docs/`
- the explicit recovery order: `progress.txt` -> active exec-plan -> `docs/DESIGN.md` -> detailed design docs as needed
- the conflict rules: repo state > memory files for current implementation truth, active exec-plan > `progress.txt` for current execution truth, latest user-confirmed intent > stale docs for target-state truth

## Consistency Rules

- Every feature named in `PRD.md` must map to flows in `APP_FLOW.md`.
- Every major flow must map to concrete implementation choices in `TECH_STACK.md` and the relevant backend design docs.
- UI decisions in the frontend design docs must be compatible with the flows in `APP_FLOW.md`.
- `DESIGN.md` must stay aligned with the detailed frontend and backend design docs.
- The active exec-plan must implement contracts already defined in the spec and design docs.
- `progress.txt` must reflect the current state implied by the active exec-plan and latest completed work.
- The active exec-plan must expand the current work tracked by `progress.txt`.
- `AGENTS.md` must point to these files instead of duplicating them.
- Feature IDs, flow IDs, design docs, and exec-plans must form a traceable chain.
- Current-state descriptions must match the repo; target-state descriptions must match the latest user-confirmed intent.
