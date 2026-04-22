# Existing Project Harness Refresh Example

Use this as a shape reference for a repository that already contains meaningful code and needs a minimal Harness.

## Step 0: Tool Detection

Inspect the project root first:

- `.claude/` present, `.codex/` absent → **Claude Code** (record as `[TARGET_TOOL]`)
- `.codex/` present, `.claude/` absent → **Codex**
- Both present → ask user which is primary
- Neither present → ask user

All generated files use paths appropriate for `[TARGET_TOOL]`.

## Inspection Flow

1. Detect `[TARGET_TOOL]` from directory structure (see above).
2. Inspect manifests, source layout, test/build commands, CI, existing docs, AI instruction file (`CLAUDE.md` or `AGENTS.md`), `progress.txt`, rules, skills, and agents.
3. Extract: platform, framework, language, verification commands, existing Harness state, sub agent setup (if any).
4. Summarize current implemented state and current Harness gaps.
5. Ask about intended target state and missing verification or AI-tooling requirements.
6. Preserve useful optional docs (`APP_FLOW.md`, `TECH_STACK.md`, `task-board.md`, `design-docs/*`).
7. Merge duplicate or drifted content into the minimal Harness docs when appropriate.
8. Create or refresh the unified verification entrypoint using `references/verify-by-stack.md`.
9. Record tool-specific paths in `docs/harness.md` Tool Adapters.

## Default Generated or Refreshed Structure

**Claude Code:**
- Refresh or create `CLAUDE.md`
- Refresh or create `progress.txt`
- Refresh or create `docs/PRD.md`
- Refresh or create `docs/DESIGN.md`
- Refresh or create `docs/dev-map.md`
- Refresh or create `docs/harness.md`
- Refresh or create `docs/exec-plans/active/YYYY-MM-DD-harness-refresh.md`
- Ensure `docs/exec-plans/completed/` exists
- Refresh or create `scripts/verify.sh` or `scripts/verify.ps1`

**Codex:**
- Same, but `AGENTS.md` instead of `CLAUDE.md`

## Verification Script Generation

Read `references/verify-by-stack.md` before writing the script. Build from three layers:

1. **Universal gates**: build, tests pass, test-count regression, no secrets, no merge markers
2. **Stack gates**: derived from detected manifests (e.g. `package.json` → Node.js gates, `.csproj` → .NET gates)
3. **Project gates**: derived from inspection — forbidden APIs, localization rules, file-length limits, sync-required configs

Record every gate name and its command in `docs/harness.md` Verification section.

## Example Delta Recording

### Current-state truth

- `docs/TECH_STACK.md` exists and is still accurate.
- No unified verification script exists.
- Tests are available through `npm test`.
- No project skills exist.
- `AGENTS.md` exists (Codex project).

### Target-state truth

- Keep `docs/TECH_STACK.md` for now but make `docs/DESIGN.md` the design entrypoint.
- Add `scripts/verify.sh` wrapping `npm ci && npm run lint && npm test` with test-count regression check.
- Add a future extension item to convert repeated release steps into `.agents/skills/release/`.

### Result

- Minimal Harness docs are created.
- Existing optional docs are referenced, not deleted.
- Verification script includes universal + Node.js stack gates.
- Future consolidation is recorded in `docs/harness.md` Extension Plan.

## Optional: Sub Agents

If the user or project complexity suggests multi-role separation, offer the compact set (3 agents) first:

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

Upgrade to the full set (7 agents) only when independent Gatekeeper and review gates are critical. Use templates from `references/sub-agents.md`.

## Usage Note

For existing projects, trust repo state for what is implemented and latest user-confirmed intent for what should exist next. Do not ask for facts the repo already answers.
