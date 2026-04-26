# Tessera Specification

> Version 1.1 · Creative Commons Attribution 4.0

This document defines the Tessera framework: how to structure a repository so that its folder tree *is* its architectural model, and how to write the `architecture.md` files that live inside it.

## 1. Core principle

**The folder structure is the architecture.** Every folder that represents an architectural element contains an `architecture.md` file describing it. There is no separate model, no DSL, no external tool of record. The filesystem is canonical.

This gives you three properties for free:

1. **Co-location** — the doc travels with the code in every PR.
2. **Single source of truth** — the folder tree can't disagree with itself.
3. **Tool-independence** — any text editor is a valid authoring environment.

## 2. The layers

Tessera borrows C4's hierarchy and binds each level to a concrete filesystem rule. A single-system repo uses four layers (L1 through L4). A **multi-system monorepo** can opt into a fifth, L0 "Landscape", which represents the whole workspace of systems.

| Level | Name | Folder rule | What it represents |
|---|---|---|---|
| L0 | **Landscape** | The repo root, in `landscape` workspace mode only | A workspace of multiple software systems (monorepo root) |
| L1 | **Context** | The repo root (default mode), or a top-level folder in landscape mode | A single software system and its external world |
| L2 | **Container** | Top-level folders under a Context | Deployable/distinct units (services, apps, databases) |
| L3 | **Component** | Folders inside containers | Logical groupings within a container |
| L4 | **Module** | Leaf folders (no subfolders) | Atomic code units — only files live here |

L0 is **opt-in** via `workspace_mode: landscape` in `.tessera/config.yaml` (see §2.2). The default is `context`, which preserves the single-system model — existing repos are unaffected.

A project with more than the configured number of levels of depth still only has those architectural layers. Extra depth collapses via the **pass-through rule** (§4).

### 2.1 What lives at each layer

**Logical code groupings live in Modules.** Modules are where application code belongs — functions, classes, handlers, view components — grouped by concern and documented as a unit. This keeps drill-down predictable: when you see a Module, you know you've reached the code.

**Runtime-pinned files may be documented at any layer (ADR-016).** Files whose path is fixed by the runtime or the ecosystem — `package.json`, `CMakeLists.txt`, `tsconfig.json`, root `README.md`, `LICENSE`, Electron `main.js`, Next.js `pages/index.js`, etc. — may be listed in a `## Files` section on the Landscape, Context, Container, or Component whose folder pins them there. This is the exception, not a pattern. Application code still belongs in Modules. See §7.5 for the pinning-rationale convention.

| Layer | `architecture.md` | Subfolders | Application code | Runtime-pinned files |
|---|---|---|---|---|
| L0 Landscape | Required (at monorepo root) | Yes (Contexts and pass-through) | No | Yes — via `## Files` with rationale |
| L1 Context | Required (at repo root in default mode; at system root in landscape mode) | Yes (Containers and pass-through) | No | Yes — via `## Files` with rationale |
| L2 Container | Required | Yes (Components and pass-through) | No | Yes — via `## Files` with rationale |
| L3 Component | Required | Yes (Modules or sub-Components) | No | Yes — via `## Files` with rationale |
| L4 Module | Required | **No** | Yes | Yes — description is optional |

If a Component accumulates *application* code files directly (`index.ts`, `handler.go`) without a pinning reason, that's a signal it should become a Module, or those files belong in a child Module. The `## Files` section at non-Module layers is specifically for files the runtime won't let you move.

### 2.2 Workspace modes: `context` vs `landscape`

Tessera has two workspace modes. Both use the same layers — they differ only in where the hierarchy starts.

- **`context` (default).** The repo root is an L1 Context. A single software system. This is what v1.0 always assumed and remains the right choice for most repositories. No config change needed — the default is implicit.
- **`landscape`.** The repo root is an L0 Landscape. Top-level folders that have `architecture.md` are L1 Contexts (independent software systems within the workspace). Use this for monorepos that host multiple products/systems.

To enable landscape mode, set `workspace_mode: landscape` in `.tessera/config.yaml`:

```yaml
# .tessera/config.yaml
workspace_mode: landscape
ignore:
  - node_modules
  - dist
```

The root `architecture.md` should then describe the landscape as a whole (what systems live here, what binds them together) and list the systems. Each top-level system folder gets its own `architecture.md` as an L1 Context.

**When to choose landscape:**
- Your repo has multiple independently-deployed products (e.g., backend + desktop + mobile + marketing site).
- You need to show cross-system relationships (which systems talk to which) in one visualization.
- The top-level folders *are* systems, not containers within one system.

**When to stay in context mode:**
- Your repo is a single system. Most repos are.
- Your top-level folders are runtime units of one system (e.g., `backend/` + `frontend/` that ship together as one product). Use Containers, not Contexts.
- You're unsure. Start in `context`; switch to `landscape` later if the modeling doesn't fit. The switch is a config edit plus root `architecture.md` rewrite — no file moves required.

See ADR-014 for the rationale and the alternatives that were considered.

## 3. Detection rules

Given a folder, its layer is determined as follows:

1. If the folder is the repo root → **Landscape** if `workspace_mode` is `landscape`, otherwise **Context**. (The root is always the top of the hierarchy, even if it contains only files — see §3.1.)
2. Else if the folder contains only files (no subfolders) → **Module**.
3. Else its layer is one level below its parent's layer.

A folder with subfolders is a *parent* element (Landscape, Context, Container, or Component). A folder with only files is a *leaf* (Module).

The depth→layer mapping for non-root folders depends on the workspace mode:

| Depth from root | `context` mode | `landscape` mode |
|---|---|---|
| 0 (root) | Context | Landscape |
| 1 | Container | Context |
| 2 | Component | Container |
| 3 | Module | Component |
| 4 | — | Module |

In both modes, a leaf folder (no subfolders) is a Module regardless of depth.

### 3.1 The flat-repo edge case

A repository with no subfolders — only source files at the root — is still an L1 Context (or an L0 Landscape, if you configured landscape mode). The root is never demoted to a Module, because the root represents the top of the architecture and cannot exist at the leaf layer by definition.

In practice, a truly flat repo is rare. If your project is a handful of scripts or a single-module library, you have two options:

- **Stay flat.** Write one `architecture.md` at the root (Context) and list the files under an optional `## Files` section, as you would for a Module. The L4 template is permitted at L1 for single-file-layer projects. This is the pragmatic choice for small libraries.
- **Introduce one Container.** Create a single top-level folder (e.g., `app/`, `src/app/`) and move your files under it. Root becomes Context, the new folder becomes a Container-or-Module. Do this only when the project grows or you anticipate more than one deployable unit.

The uniform children rule (§5) does not apply to flat-repo roots because a root with no subfolders has no architectural children to unify.

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

### 7.0 L0 Landscape (monorepo root, `landscape` mode only)

```markdown
# [Workspace Name]

## Overview
What this system landscape is — the portfolio of software systems that live
in this workspace, and what binds them together (shared users, shared data
boundaries, shared deployment pipeline, etc.).

## Systems
- **[System Name]**: One-line description of what it does
- **[System Name]**: One-line description of what it does

## External Systems
- **[External System]**: How the landscape interacts with it

## Actors
- **[Actor Name]**: Which systems they interact with, and how

## Metadata
- **Layer**: Landscape
- **Tags**: [tags]
- **Owner**: @username
- **Status**: Active | Planned | Deprecated

## Key Decisions
- [Decision and rationale — include ADR links where they exist]
```

Only use this template at the repo root when `workspace_mode: landscape` is set in `.tessera/config.yaml`. In the default `context` mode the repo root uses the L1 Context template below.

### 7.1 L1 Context (repo root in default mode; system root in landscape mode)

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

### 7.5 `## Files` at non-Module layers (pinning rationale)

A Landscape, Context, Container, or Component may include an optional `## Files` section listing files physically pinned at that folder's root — files the runtime or ecosystem requires at that exact location. The format matches the Module `## Files` section.

**Each entry at a non-Module layer must include a pinning rationale in its description** — a short "why is this file here?" note. A file without a rationale is surfaced as a soft drift warning by `validate_files` and the drift-tax footer.

```markdown
## Files
- `package.json` — npm manifest. Pinned: required at package root by npm.
- `tsconfig.json` — TypeScript compiler config. Pinned: referenced by `tsc` from project root.
- `main.js` — Electron main-process entry. Pinned: referenced by `package.json` `"main"` field.
```

Rationales keep the exception visible. A reader looking at the file list asks "why isn't this in a Module?" and the answer is right there. Without rationales, a non-Module `## Files` section drifts from "runtime-pinned exceptions" toward "a second place to list any file," which defeats the purpose.

If the only thing pinning a file to a non-Module folder is convention you can change — move it into a Module instead. The section is for files the runtime won't let you relocate, not for convenience.

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

### 8.1 Keeping the graph consistent

Bilateral maintenance is the framework's weak point: if one side updates and the other forgets, the graph drifts. Three ways to prevent that:

1. **Automate it.** If you use the Tessera MCP server, `update_element` can auto-heal the reverse link when you add or remove a dependency. Prefer this over manual edits in any non-trivial project.
2. **Validate on commit.** Add a pre-commit hook that runs a link-consistency check. The Tessera MCP's `check_links` tool does this; without tooling, a simple script that parses `Depends on` and `Depended by` across the tree is ~30 lines. Block commits that leave the graph inconsistent.
3. **Validate in CI.** Same check, run on every pull request. Fail the build on drift. This is the minimum for teams — individual discipline erodes; CI doesn't.

List specific targets, not aggregates. `"Depended by: all modules in this container"` is not a valid entry — it cannot be mechanically checked and will decay into a lie. Write out each link.

### 8.2 Cross-cutting dependencies

For an element that almost everything depends on (e.g., a shared `types/` or `constants/` module), listing every dependent is noisy but still preferred. If the `Depended by` list grows past a screen, that's a signal the element is too broad — consider splitting it, or accept the noise in exchange for accuracy. Do not paper over it with "all modules".

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

This is Tessera Spec v1.1. The versioning policy:

- **Patch (v1.1.x):** clarifications, typos, new examples. No migration needed.
- **Minor (v1.x):** new optional template sections, new metadata fields, new layer detection edge cases that don't change the meaning of existing trees. Existing repos stay valid.
- **Major (v2.0):** changes to the layer model, the pass-through rule, the uniform children rule, or the detection algorithm. A migration note will ship with any v2.0 release describing the conversion from v1 trees.

Implementations should declare which spec version they target. ADR-style tooling (like the Tessera MCP server) should accept a version hint and refuse to operate on trees newer than they understand.

---

*Tessera is published under Creative Commons Attribution 4.0 International. Adopt it, fork it, write your own tools. Just credit the source.*
