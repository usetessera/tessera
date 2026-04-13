# Tessera Specification

> Version 1.0 · Creative Commons Attribution 4.0

This document defines the Tessera framework: how to structure a repository so that its folder tree *is* its architectural model, and how to write the `architecture.md` files that live inside it.

## 1. Core principle

**The folder structure is the architecture.** Every folder that represents an architectural element contains an `architecture.md` file describing it. There is no separate model, no DSL, no external tool of record. The filesystem is canonical.

This gives you three properties for free:

1. **Co-location** — the doc travels with the code in every PR.
2. **Single source of truth** — the folder tree can't disagree with itself.
3. **Tool-independence** — any text editor is a valid authoring environment.

## 2. The four layers

Tessera borrows C4's four-level hierarchy and binds each level to a concrete filesystem rule.

| Level | Name | Folder rule | What it represents |
|---|---|---|---|
| L1 | **Context** | The repo root | The whole system and its external world |
| L2 | **Container** | Top-level folders under root | Deployable/distinct units (services, apps, databases) |
| L3 | **Component** | Folders inside containers | Logical groupings within a container |
| L4 | **Module** | Leaf folders (no subfolders) | Atomic code units — only files live here |

A project with more than four levels of depth still only has four architectural layers. Extra depth collapses via the **pass-through rule** (§4).

## 3. Detection rules

Given a folder, its layer is determined as follows:

1. If the folder is the repo root → **Context**.
2. Else if the folder contains only files (no subfolders) → **Module**.
3. Else its layer is one level below its parent's layer.

A folder with subfolders is a *parent* element (Context, Container, or Component). A folder with only files is a *leaf* (Module).

## 4. The pass-through rule

Not every folder in a codebase is an architectural element. Conventional scaffolding folders like `src/`, `lib/`, `app/`, `pages/`, `internal/` exist for language/framework reasons, not architectural ones.

**A folder without an `architecture.md` is not an architectural element.** Its children are promoted to the parent's layer.

Example:

```
backend/
├── architecture.md         ← Container
└── src/                    ← NO architecture.md — passes through
    ├── api/
    │   └── architecture.md ← Component (child of backend, not src)
    └── db/
        └── architecture.md ← Component (child of backend, not src)
```

The pass-through rule lets you follow your language's idiomatic structure without polluting the architecture tree with phantom elements.

## 5. The uniform children rule

All direct architectural children of an element must be the same layer.

- A Container's architectural children must all be Components. Not a mix of Components and Modules.
- A Component's architectural children must all be sub-Components or all Modules. Not a mix.

If you find yourself wanting to mix layers, either:
- **Promote** the Module to a Component (give it subfolders), or
- **Demote** the Component to a Module (flatten it).

This keeps drill-down predictable: at every level, you know what type of element you're looking at.

## 6. Module rule

**Module folders must not contain subfolders.** If a Module grows to need subdivision, it should be promoted to a Component and its contents reorganized into new Module children.

This is the only structural constraint Tessera places on your code.

## 7. `architecture.md` templates

Each layer has a template. Use it. Keeping the format uniform makes the tree easy for humans and AI agents to read.

### 7.1 L1 Context (repo root)

```markdown
# [System Name]

## Overview
What the system is, its purpose, the problem it solves.

## External Systems
- **[System Name]**: Description of the interaction

## Actors
- **[Actor Name]**: Role and interaction pattern

## Metadata
- **Layer**: Context
- **Tags**: [tags]
- **Owner**: @username
- **Status**: Active | Planned | Deprecated

## Key Decisions
- [Decision and rationale]
```

### 7.2 L2 Container

```markdown
# [Container Name]

## Overview
What this container does and its role in the system.

## Technology
- **Runtime**: e.g., Node.js 20
- **Framework**: e.g., React, Express
- **Data store**: if applicable

## Metadata
- **Layer**: Container
- **Tags**: [tags]
- **Depends on**: [relative link to architecture.md]
- **Depended by**: [relative link to architecture.md]
- **Owner**: @username
- **Status**: Active | Planned | Deprecated

## API Surface
- Public interfaces this container exposes

## Deployment
- How and where this is deployed

## Key Decisions
- [Decision and rationale]
```

### 7.3 L3 Component

```markdown
# [Component Name]

## Overview
What this component does within its parent container.

## Metadata
- **Layer**: Component
- **Tags**: [tags]
- **Depends on**: [relative link]
- **Depended by**: [relative link]
- **Owner**: @username
- **Status**: Active | Planned | Deprecated

## Interfaces
- Public API or contract this component exposes

## Key Decisions
- [Decision and rationale]
```

### 7.4 L4 Module

```markdown
# [Module Name]

## Overview
What this module does and why it exists.

## Files
- `filename.ext` — Description of what this file does

## Functions
- `functionName(params): ReturnType` — Description

## Classes
- `ClassName` — Description and responsibilities

## Metadata
- **Layer**: Module
- **Tags**: [tags]
- **Depends on**: [relative link]
- **Depended by**: [relative link]
- **Owner**: @username
- **Status**: Active | Planned | Deprecated

## Key Decisions
- [Decision and rationale]
```

## 8. Relationships

Dependencies between elements are expressed by **Depends on** and **Depended by** lists in the metadata. Use relative paths to the other element's `architecture.md`.

When you add a dependency, update **both** elements' files. This is the one place Tessera requires bilateral updates — without it, the dependency graph is unreliable.

Example:

```markdown
# In backend/api/architecture.md
- **Depends on**: [../db/architecture.md](../db/architecture.md)

# In backend/db/architecture.md
- **Depended by**: [../api/architecture.md](../api/architecture.md)
```

## 9. Maintenance rules

The framework lives or dies on whether `architecture.md` files stay current. Five rules:

1. **When you modify code, check the sibling `architecture.md`.** If you added a function, changed an interface, renamed something, or altered behavior — update it.
2. **When you create a folder representing an architectural element, create its `architecture.md` immediately** using the appropriate template.
3. **When you add or remove a dependency, update both elements.**
4. **Before significant changes, read the current `architecture.md` first.** Context and rationale live there.
5. **Never delete an `architecture.md` without also deleting or deprecating the element it describes.**

## 10. Decision records (ADRs)

For decisions that are significant, cross-cutting, or likely to be questioned later, write an **Architecture Decision Record**. ADRs live in `adrs/` at the repo root, numbered sequentially (`001-framework-choice.md`, `002-database-choice.md`, ...).

ADR template:

```markdown
# ADR-[NNN]: [Title]

**Date**: YYYY-MM-DD
**Status**: Accepted | Superseded by ADR-NNN | Deprecated
**Deciders**: [who]

## Context and Problem Statement
What problem are we solving, and why does it matter now?

## Considered Options
- **Option A** — brief description
- **Option B** — brief description
- **Option C** — brief description

## Decision
Chosen option: **Option A**, because [one-line justification].

[Additional reasoning if needed.]

## Consequences
**Positive:**
- ...

**Negative:**
- ...
```

Link relevant ADRs from the `Key Decisions` section of affected `architecture.md` files.

## 11. Ignoring folders

Some folders should never be treated as architectural elements even if they contain other folders: `node_modules/`, `.git/`, `dist/`, `build/`, `__pycache__/`, `target/`, `vendor/`, `.venv/`, etc.

The convention is to maintain an ignore list in `.tessera/config.yaml` at the repo root (only required if you're using Tessera tooling). Without tooling, just don't put `architecture.md` files in those folders — the pass-through rule will make them invisible.

## 12. What Tessera is not

- **Not a diagram tool.** Diagrams are output, not input. Generate them from the tree if you want.
- **Not a governance framework.** Tessera describes your architecture; it doesn't enforce it.
- **Not a replacement for ADRs, RFCs, or design docs.** Use those for proposals and decisions. Tessera captures the *current* state.
- **Not tied to any language, framework, or deployment model.** If it has a filesystem, Tessera works.

## 13. Versioning

This is Tessera Spec v1.0. Breaking changes to the spec will be versioned. Implementations should declare which version they target.

---

*Tessera is published under Creative Commons Attribution 4.0 International. Adopt it, fork it, write your own tools. Just credit the source.*
