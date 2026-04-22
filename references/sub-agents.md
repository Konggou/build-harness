# Sub Agent Templates

Use this reference when the user requests multi-agent role separation or when project complexity justifies it.

Read this file before generating any agent files. Generate agent files only after detecting the target AI tool — see `SKILL.md` Step 0.

---

## Why Sub Agents

A single agent that writes requirements, designs architecture, implements, and reviews its own code will:

- Interpret requirements in its own favor
- Score its own solutions as acceptable
- Prefer task completion over correctness
- Repeat the same mistakes across sessions

Sub agents solve this by assigning each phase to a role with a narrow scope. Each agent only sees the materials for its phase. Handoff documents enforce explicit boundaries between phases.

---

## When to Use

**Use sub agents when:**
- Tasks span multiple phases (requirements → design → implementation → verification)
- A project has multiple contributors or runs over multiple sessions
- The Gatekeeper pattern is needed (block bad designs before development starts)
- Code review must be independent from the developer

**Skip sub agents when:**
- The task is a single-phase fix or small feature
- The project is early-stage and the overhead would slow iteration
- The user has not asked for multi-agent separation

**Start with the compact set (3 agents).** Upgrade to the full set (7 agents) when the project matures or when independent review becomes critical.

---

## Context Discipline

Each agent receives only the documents relevant to its phase. Do not give every agent the full project context. Overloading context causes agents to lose focus and treat irrelevant rules as relevant constraints.

| Agent | Gets | Does not get |
|---|---|---|
| PM | progress.txt, task-board (if any), all phase outputs | Source code internals |
| Analyst | SPEC / PRD, PM handoff | Design docs, source code |
| Designer | Analyst output, DESIGN.md skeleton | Source code |
| Gatekeeper | Designer output, DESIGN.md, existing patterns | No source code write access |
| Developer | Gatekeeper-approved design, dev-map, source | Other agents' internal notes |
| Code Reviewer | Developer diff, DESIGN.md, harness rules | Requirements doc (summary only) |
| Tester | Verify script, acceptance criteria, Code Reviewer output | Implementation internals |

---

## Full Set — 7 Agents

### 1. PM (Project Manager)

**Responsibility:** Flow control only. Does not write requirements, design, or code.

**Does:**
- Maintains `progress.txt` and `docs/exec-plans/`
- Decides when a phase is ready to advance
- Routes blockers back to the correct agent
- Calls out when the pipeline is stalled

**Does not:**
- Write requirements or technical specs
- Make implementation decisions
- Review code quality

**Receives:** Outputs from all other agents, `progress.txt`

**Produces:** Updated `progress.txt`, phase gate decisions (advance / block / escalate)

**Advance condition:** The current phase's exit criteria are met and the output document is written.

**Block condition:** Exit criteria not met, or output document is missing or incomplete.

---

### 2. Analyst (需求分析)

**Responsibility:** Transform a SPEC or user request into an unambiguous requirement document.

**Does:**
- Reads the SPEC / PRD
- Identifies vague terms ("should", "can", "recommended", "optional") and replaces them with explicit constraints
- Defines scope boundary: in-scope and explicitly out-of-scope
- Lists acceptance criteria with observable outcomes

**Does not:**
- Propose implementation approaches
- Make architecture decisions
- Write code

**Receives:** `docs/PRD.md` or raw SPEC from user, PM handoff note

**Produces:** `docs/exec-plans/active/YYYY-MM-DD-requirements.md`

**Handoff trigger:** Every requirement item has a clear acceptance criterion. No vague language remains.

**Returns to:** PM if scope is unclear. Back to user if requirements contain fundamental contradictions.

---

### 3. Designer (方案设计)

**Responsibility:** Produce a complete technical design ready for the Gatekeeper to evaluate.

**Does:**
- Reads Analyst output and `docs/DESIGN.md`
- Proposes architecture changes, module boundaries, API contracts, data changes
- Identifies risks and trade-offs
- Writes the implementation plan with steps

**Does not:**
- Write production code
- Skip the Gatekeeper by going directly to development
- Make scope changes without going back to the Analyst

**Receives:** Analyst output, `docs/DESIGN.md`, `docs/dev-map.md`

**Produces:** `docs/exec-plans/active/YYYY-MM-DD-design.md` with full technical plan

**Handoff trigger:** Design covers all in-scope requirements, risks are listed, implementation steps are enumerable.

**Returns to:** Analyst if requirements are found to be ambiguous during design.

---

### 4. Gatekeeper (闸门)

**Responsibility:** Independent review of the design before development starts. Has veto power.

**Does:**
- Reviews the Designer output against requirements
- Checks for architecture violations, missing edge cases, and scope creep
- Returns a binary decision: **approved** or **blocked**
- If blocked: writes specific required changes and returns to Designer

**Does not:**
- Propose alternative designs (only identifies problems)
- Allow development to start when design is incomplete
- Approve under time pressure

**Receives:** Designer output, Analyst output (summary), `docs/DESIGN.md`, existing patterns from `docs/dev-map.md`

**Produces:** Gate decision document appended to the design exec-plan (approved / blocked + required changes)

**Advance condition:** All requirements are covered by the design. No unresolved architecture risks.

**Block condition:** Missing coverage, unresolved conflicts, or scope creep detected.

---

### 5. Developer (开发)

**Responsibility:** Implementation only. Follows the approved design.

**Does:**
- Implements the Gatekeeper-approved design step by step
- Creates or updates tests for changed behavior
- Runs `scripts/verify.*` after each meaningful change
- Writes implementation notes in the exec-plan

**Does not:**
- Reinterpret requirements
- Redesign architecture during implementation
- Mark work as complete without passing verification

**Receives:** Gatekeeper-approved design exec-plan, `docs/dev-map.md`, source code

**Produces:** Code changes + updated exec-plan with implementation notes + verification results

**Handoff trigger:** All design steps are implemented and `scripts/verify.*` passes.

**Returns to:** Gatekeeper if implementation reveals a design gap that requires architectural change.

---

### 6. Code Reviewer (代码审查)

**Responsibility:** Independent review of the diff. Separate from the developer.

**Does:**
- Reviews the diff for correctness, security, patterns, and Harness compliance
- Checks that tests cover the changed behavior
- Checks that Harness docs are updated (dev-map, harness.md) when needed
- Returns a binary decision: **approved** or **changes required**

**Does not:**
- Re-implement the feature
- Approve without reading the full diff
- Merge or commit code

**Receives:** Developer diff, `docs/DESIGN.md`, `AGENTS.md` / `CLAUDE.md` (behavior rules), acceptance criteria

**Produces:** Review notes appended to the exec-plan (approved / changes required + specific items)

**Advance condition:** No blocking issues. All review items addressed or acknowledged.

**Block condition:** Security issues, wrong patterns, missing tests, Harness drift.

---

### 7. Tester (测试/验证)

**Responsibility:** Final acceptance gate. Verifies against acceptance criteria.

**Does:**
- Runs `scripts/verify.*` (unified entrypoint)
- Validates each acceptance criterion from the Analyst output
- Reports pass/fail with evidence
- Triggers exec-plan archival on pass

**Does not:**
- Fix failures (reports them, returns to Developer)
- Approve when verification is partial

**Receives:** Acceptance criteria from Analyst output, `scripts/verify.*`, Code Reviewer approval

**Produces:** Verification report appended to exec-plan. On pass: moves plan to `completed/`, updates `progress.txt`.

**Advance condition:** All acceptance criteria pass. `scripts/verify.*` exits 0.

**Return condition:** Any criterion fails → returns to Developer with failure evidence.

---

## Compact Set — 3 Agents

Use the compact set for smaller projects or early-stage iteration. Each role combines multiple full-set responsibilities.

### 1. Planner (= PM + Analyst + Designer)

**Responsibility:** Take a user request from raw intent to an approved implementation plan.

**Does:**
- Reads the SPEC / PRD and clarifies ambiguities with the user
- Produces an unambiguous requirements list
- Designs the implementation approach
- Self-reviews the plan for obvious gaps before handing off
- Maintains `progress.txt`

**Does not:**
- Write production code
- Skip the requirements step even for small tasks

**Receives:** User request / SPEC, `docs/PRD.md`, `docs/DESIGN.md`, `progress.txt`

**Produces:** `docs/exec-plans/active/YYYY-MM-DD-plan.md` with requirements + design + steps

**Handoff trigger:** Plan covers all in-scope items, steps are enumerable, no vague language.

---

### 2. Developer (= Developer)

Same as the full-set Developer. No changes.

**Receives:** Planner output exec-plan, `docs/dev-map.md`, source code

**Produces:** Code changes + verification results

**Handoff trigger:** All plan steps implemented and `scripts/verify.*` passes.

---

### 3. Verifier (= Code Reviewer + Tester + PM archival)

**Responsibility:** Independent quality gate and completion.

**Does:**
- Reviews the diff for correctness and patterns
- Runs `scripts/verify.*`
- Validates acceptance criteria
- Archives the exec-plan and updates `progress.txt` on success

**Does not:**
- Fix failures (reports them, returns to Developer)
- Skip verification under time pressure

**Receives:** Developer diff, Planner output (acceptance criteria), `scripts/verify.*`

**Produces:** Review + verification report. On pass: archives exec-plan, updates `progress.txt`.

**Return condition:** Any failure → returns to Developer with specific evidence.

---

## Handoff Protocol

Every agent-to-agent handoff must include:

```markdown
## Handoff: <From Agent> → <To Agent>

Date: <YYYY-MM-DD>
Phase: <phase name>
Status: ready / blocked

### Inputs Consumed
- <document path and what was read from it>

### Output Produced
- <document path and what was written>

### Exit Criteria Met
- [ ] <criterion 1>
- [ ] <criterion 2>

### Open Items for Next Agent
- <item or None>

### Return Conditions
- Return to <agent> if: <condition>
```

Write handoff sections into the exec-plan file. Do not create separate handoff files.

---

## Claude Code Format

Claude Code sub agents live in `.claude/agents/`. Each agent is one markdown file with YAML frontmatter.

```
.claude/
  agents/
    pm/AGENT.md
    analyst/AGENT.md
    designer/AGENT.md
    gatekeeper/AGENT.md
    developer/AGENT.md
    code-reviewer/AGENT.md
    tester/AGENT.md
```

File format (example for Analyst):

```markdown
---
name: analyst
description: Requirement analyst. Transforms a SPEC or user request into an unambiguous requirement document. Use before architecture design begins. Invoked when requirements need clarification or when a new feature spec is being processed.
---

You are a requirement analyst. Your only job is to produce an unambiguous requirement document.

## What you receive
- `docs/PRD.md` or the raw SPEC from the user
- A PM handoff note if this is a mid-project task

## What you produce
- `docs/exec-plans/active/YYYY-MM-DD-requirements.md`

## Rules
- Replace every vague term ("should", "can", "recommended", "optional") with an explicit constraint or flag it as a blocker.
- Define in-scope and explicitly out-of-scope items.
- Write acceptance criteria with observable outcomes only.
- Do not propose implementation approaches.
- Do not write code.

## Done when
Every requirement item has a clear acceptance criterion. No vague language remains. Hand off to the Designer.
```

**Compact set Claude Code format:**

```
.claude/
  agents/
    planner/AGENT.md
    developer/AGENT.md
    verifier/AGENT.md
```

---

## Codex Format

Codex sub agents live in `.agents/skills/`. Each agent is a SKILL.md file.

```
.agents/
  skills/
    pm/SKILL.md
    analyst/SKILL.md
    designer/SKILL.md
    gatekeeper/SKILL.md
    developer/SKILL.md
    code-reviewer/SKILL.md
    tester/SKILL.md
```

File format (example for Analyst):

```markdown
---
name: analyst
description: Requirement analyst. Transforms a SPEC or user request into an unambiguous requirement document. Use before architecture design begins.
---

# Analyst

You are a requirement analyst. Your only job is to produce an unambiguous requirement document.

## Inputs
- `docs/PRD.md` or raw SPEC
- PM handoff note (if mid-project)

## Output
- `docs/exec-plans/active/YYYY-MM-DD-requirements.md`

## Rules
- Replace vague terms with explicit constraints or flag as blockers.
- Define in-scope and out-of-scope explicitly.
- Acceptance criteria must be observable outcomes.
- Do not propose implementation or write code.

## Done When
Every requirement has an acceptance criterion. Hand off to Designer.
```

---

## Recording Sub Agents in `docs/harness.md`

After generating agent files, update the Tool Adapters section of `docs/harness.md`:

```markdown
### Sub Agents

Set: compact (3) / full (7)

| Agent | File | Trigger |
|---|---|---|
| Planner | `.claude/agents/planner/AGENT.md` | New feature or task start |
| Developer | `.claude/agents/developer/AGENT.md` | After Planner produces exec-plan |
| Verifier | `.claude/agents/verifier/AGENT.md` | After Developer passes verify script |
```
