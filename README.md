# Build Spec Skill

English is the default language for this repository. A Chinese version is included below.

## Overview

`build-spec` is a Codex skill for turning an empty project or an existing repository into a layered documentation system before implementation work continues.

It is designed to:

- inspect the current project state first
- ask blocking Planning-mode questions until high-impact unknowns are removed
- generate a structured spec system under `docs/`
- maintain lightweight and detailed execution memory through `progress.txt` and `docs/exec-plans/`
- generate a concise `AGENTS.md` that tells future AI sessions how to recover context safely

## What It Produces

Top-level outputs:

- `docs/PRD.md`
- `docs/APP_FLOW.md`
- `docs/TECH_STACK.md`
- `docs/DESIGN.md`
- `progress.txt`
- `AGENTS.md`

Detailed design:

- `docs/design-docs/frontend/`
- `docs/design-docs/backend/`

Detailed execution memory:

- `docs/exec-plans/active/`
- `docs/exec-plans/completed/`

## Key Behaviors

- Strong trigger phrases include `构建文档索引` and `构建spec`
- Empty projects are interrogated from first principles before any implementation decisions are documented
- Existing projects are inspected first so the skill does not ask for facts the repo already answers
- `progress.txt` stays short and points to the active execution plan
- Detailed implementation history stays in `docs/exec-plans/`
- `DESIGN.md` stays short and points to detailed frontend and backend design docs
- Feature IDs and flow IDs are used to keep requirements, flows, design, and execution plans traceable

## Repository Layout

```text
.
├── README.md
└── build-spec/
    ├── SKILL.md
    ├── agents/
    │   └── openai.yaml
    └── references/
        ├── doc-set.md
        ├── inspection-checklist.md
        ├── examples/
        └── templates/
```

## Install

To install manually into Codex global skills:

1. Copy the `build-spec/` directory into `C:\Users\user\.codex\skills\build-spec`
2. Ensure the installed folder contains `SKILL.md`, `agents/openai.yaml`, and `references/`
3. Restart the relevant Codex session if the skill list is cached

## Usage

Example prompts:

- `Use $build-spec to inspect this project, ask blocking Planning-mode questions, and generate the layered spec system.`
- `构建文档索引`
- `构建spec`

## Validation

This repository was validated with the `skill-creator` validator:

```powershell
python "C:\Users\user\.codex\skills\.system\skill-creator\scripts\quick_validate.py" "<path-to-build-spec>"
```

## Chinese

以下是中文说明，英文仍然是默认版本。

## 概述

`build-spec` 是一个 Codex skill，用来在继续实现之前，把空项目或已有项目整理成一套分层的文档系统。

它会：

- 先检查当前项目状态
- 在 Planning 模式下持续追问，直到高影响的不确定项被消除
- 在 `docs/` 下生成结构化规格文档
- 通过 `progress.txt` 和 `docs/exec-plans/` 维护轻量与详细两层执行记忆
- 生成精简的 `AGENTS.md`，让后续 AI 会话能安全恢复上下文

## 产物

顶层文档：

- `docs/PRD.md`
- `docs/APP_FLOW.md`
- `docs/TECH_STACK.md`
- `docs/DESIGN.md`
- `progress.txt`
- `AGENTS.md`

详细设计：

- `docs/design-docs/frontend/`
- `docs/design-docs/backend/`

详细执行记忆：

- `docs/exec-plans/active/`
- `docs/exec-plans/completed/`

## 关键特性

- 强触发词包括 `构建文档索引` 和 `构建spec`
- 对空项目会先从第一性原理追问，再生成文档
- 对已有项目会先读代码和结构，避免重复向用户问 repo 已经能回答的问题
- `progress.txt` 保持轻量，只记录当前状态并指向 active exec-plan
- 详细实现历史保留在 `docs/exec-plans/`
- `DESIGN.md` 保持精简，只负责索引详细前后端设计
- 通过功能 ID 与流程 ID 保证需求、流程、设计、执行计划之间可追踪

## 安装

手动安装到 Codex 全局 skill 目录：

1. 把 `build-spec/` 复制到 `C:\Users\user\.codex\skills\build-spec`
2. 确认目录中包含 `SKILL.md`、`agents/openai.yaml` 和 `references/`
3. 如果 Codex 缓存了 skill 列表，重开相关会话
