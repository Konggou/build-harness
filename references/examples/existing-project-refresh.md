# Existing Project Refresh Example

Use this as a shape reference for a repository that already contains meaningful code and needs its docs and execution memory refreshed.

## Generated or Refreshed Structure

- Refresh `docs/PRD.md`
- Refresh `docs/APP_FLOW.md`
- Refresh `docs/TECH_STACK.md`
- Refresh `docs/DESIGN.md`
- Refresh or add targeted files under `docs/design-docs/frontend/`
- Refresh or add targeted files under `docs/design-docs/backend/`
- Refresh `progress.txt`
- Refresh or replace the authoritative active exec-plan
- Update `AGENTS.md`

## Example Refresh Flow

1. Inspect manifests, routes, services, schema, and existing docs.
2. Summarize current implemented state.
3. Ask about intended future state and missing requirements.
4. Mark deltas between current implementation and desired target state.
5. Rewrite drifted docs while preserving still-valid content.
6. Rebuild `progress.txt` so it points to the current authoritative active exec-plan.

## Example Delta Recording

### Current-state truth

- `F-1` exists in code but lacks complete docs
- `FLOW-2` exists but error handling is undocumented

### Target-state truth

- User confirms `F-3` should be added
- User confirms the dashboard flow should change after login

### Result

- Docs describe both current state and target delta explicitly where needed
- Active exec-plan focuses only on the remaining implementation or cleanup work

## Usage Note

For existing projects, trust repo state for what is implemented and latest user-confirmed intent for what should exist next.
