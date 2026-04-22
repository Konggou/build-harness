# Build Harness

English is the default README language for this repository. A Chinese version is included below.

## Overview

`build-harness` is a skill for **Claude Code** and **Codex** that turns an empty project or an existing repository into a minimal usable AI Harness.

It detects which AI tool the project targets, preserves useful existing docs, asks only the questions that cannot be answered from the repo, and generates a small, durable document and tooling set that lets AI work with stable context, rules, repeatable workflows, and verifiable outcomes.

## What It Produces by Default

| Output | Claude Code | Codex |
|---|---|---|
| AI instruction file | `CLAUDE.md` | `AGENTS.md` |
| Execution memory | `progress.txt` | `progress.txt` |
| Spec layer | `docs/PRD.md`, `docs/DESIGN.md` | same |
| Navigation | `docs/dev-map.md` | same |
| Harness entrypoint | `docs/harness.md` | same |
| Execution plans | `docs/exec-plans/active/`, `docs/exec-plans/completed/` | same |
| Verification | `scripts/verify.sh` or `scripts/verify.ps1` | same |

Optional outputs (generated only when justified):

| Output | Claude Code | Codex |
|---|---|---|
| Complex UI flows | `docs/APP_FLOW.md` | same |
| Detailed architecture | `docs/design-docs/*` | same |
| Task portfolio | `docs/task-board.md` | same |
| Path-scoped rules | `.claude/rules/*.md` | `.codex/rules/*.rules` |
| Permissions | `.claude/settings.json` | — |
| Project skills | `.claude/skills/<name>/SKILL.md` | `.agents/skills/<name>/SKILL.md` |
| Sub agents | `.claude/agents/<name>/AGENT.md` | `.agents/skills/<name>/SKILL.md` |

## Key Behaviors

- **Auto-detects AI tool**: `.claude/` → Claude Code; `.codex/` → Codex; both or neither → asks user
- **Inspect-first**: reads repo structure before asking questions; does not ask for facts the repo can answer
- **Tech-stack-aware verification**: generates `scripts/verify.*` with real gates derived from detected stack (build, lint, test, test-count regression, project-specific forbidden patterns)
- **Sub agent support**: optionally generates a compact (3-agent) or full (7-agent) pipeline — Planner, Developer, Verifier or PM/Analyst/Designer/Gatekeeper/Developer/Reviewer/Tester
- **Minimal by default**: does not generate `APP_FLOW.md`, `TECH_STACK.md`, `task-board.md`, or `design-docs/*` unless complexity justifies it
- **Execution memory**: `progress.txt` stays short; detailed plans live in `docs/exec-plans/`

## Repository Layout

```text
.
├── README.md
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── doc-set.md
    ├── inspection-checklist.md
    ├── sub-agents.md
    ├── verify-by-stack.md
    ├── examples/
    │   ├── empty-project-bootstrap.md
    │   └── existing-project-refresh.md
    └── templates/
        ├── prd-template.md
        ├── design-template.md
        ├── dev-map-template.md
        ├── harness-template.md
        ├── claude-md-template.md
        ├── agents-md-template.md
        ├── progress-template.txt
        ├── active-exec-plan-template.md
        ├── completed-exec-plan-template.md
        ├── app-flow-template.md
        ├── task-board-template.md
        └── detailed-design-template.md
```

## Install

### Claude Code

Copy this repository into your Claude Code skills directory:

```
~/.claude/skills/build-harness/
```

Ensure the installed folder contains `SKILL.md`, `agents/openai.yaml`, and `references/`.

### Codex

Copy this repository into your Codex skills directory:

```
~/.codex/skills/build-harness/
```

Ensure the installed folder contains `SKILL.md`, `agents/openai.yaml`, and `references/`.

Restart the relevant session if the skill list is cached.

## Usage

Example prompts:

- `build harness` / `setup harness` / `init harness`
- `Use $build-harness to inspect this project and build a minimal usable Harness.`
- `构建harness` / `构建spec` / `构建文档索引`

## Validation

This repository can be validated with the `skill-creator` validator:

```powershell
python "<skill-creator-path>/scripts/quick_validate.py" "<path-to-build-harness>"
```

---

## 中文说明

## 概述

`build-harness` 是一个同时支持 **Claude Code** 和 **Codex** 的 skill，用来把空项目或已有项目整理成一套最小可用 AI Harness。

它会自动检测目标 AI 工具，保留有价值的现有文档，只追问仓库无法回答的问题，并生成一套精简、持久的文档和工具集，让 AI 在稳定的上下文、规则、可复用工作流和可验证结果下工作。

## 默认产物

| 产物 | Claude Code | Codex |
|---|---|---|
| AI 指令文件 | `CLAUDE.md` | `AGENTS.md` |
| 执行记忆 | `progress.txt` | `progress.txt` |
| 规格层 | `docs/PRD.md`、`docs/DESIGN.md` | 同左 |
| 导航地图 | `docs/dev-map.md` | 同左 |
| Harness 入口 | `docs/harness.md` | 同左 |
| 执行计划 | `docs/exec-plans/active/`、`docs/exec-plans/completed/` | 同左 |
| 验证脚本 | `scripts/verify.sh` 或 `scripts/verify.ps1` | 同左 |

按需生成（仅在有充分理由时才创建）：

| 产物 | Claude Code | Codex |
|---|---|---|
| 复杂 UI 流程 | `docs/APP_FLOW.md` | 同左 |
| 详细架构设计 | `docs/design-docs/*` | 同左 |
| 任务看板 | `docs/task-board.md` | 同左 |
| 路径规则 | `.claude/rules/*.md` | `.codex/rules/*.rules` |
| 权限控制 | `.claude/settings.json` | — |
| 项目 Skills | `.claude/skills/<name>/SKILL.md` | `.agents/skills/<name>/SKILL.md` |
| Sub Agents | `.claude/agents/<name>/AGENT.md` | `.agents/skills/<name>/SKILL.md` |

## 关键特性

- **自动检测 AI 工具**：有 `.claude/` → Claude Code；有 `.codex/` → Codex；都有或都没有 → 询问用户
- **先读代码再提问**：读取仓库结构后再追问，不问仓库已能回答的问题
- **技术栈感知验证**：根据检测到的技术栈生成有实质内容的 `scripts/verify.*`（构建、lint、测试、测试数量回归、项目专属禁止模式）
- **Sub Agent 支持**：可选择生成精简版（3个 agent）或完整版（7个 agent）的多角色流水线
- **最小化原则**：不默认生成 `APP_FLOW.md`、`TECH_STACK.md`、`task-board.md` 或 `design-docs/*`
- **两层执行记忆**：`progress.txt` 保持轻量，详细计划放在 `docs/exec-plans/`

## 安装

### Claude Code

将本仓库复制到 Claude Code skills 目录：

```
~/.claude/skills/build-harness/
```

### Codex

将本仓库复制到 Codex skills 目录：

```
~/.codex/skills/build-harness/
```

确保安装目录包含 `SKILL.md`、`agents/openai.yaml` 和 `references/`。如果 skill 列表已缓存，请重启相关会话。

## 使用方式

示例触发词：

- `build harness` / `setup harness` / `init harness`
- `Use $build-harness to inspect this project and build a minimal usable Harness.`
- `构建harness` / `构建spec` / `构建文档索引`
