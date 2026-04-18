# Tessera Examples

Worked examples of the Tessera specification at different scales.

- **[tiny-todo-app/](./tiny-todo-app/)** — single-container app, ~4 architectural elements. The minimum viable tree.
- **[medium-web-app/](./medium-web-app/)** — frontend + backend + worker, ~12 elements. Shows pass-through, dependencies, and ADRs.
- **[monorepo-landscape/](./monorepo-landscape/)** — multi-system monorepo using **L0 Landscape** mode (three independent Contexts under one root). Demonstrates `workspace_mode: landscape`, cross-system dependencies, and when to choose landscape mode over single-system mode.
- **Real-world** — this repository itself. [Tessera's own source](https://github.com/usetessera/tessera) is structured as a Tessera tree. Browse it for a production-grade example: three containers, multiple components per container, and every folder carries its own `architecture.md`.

Each example is a working, file-by-file illustration — not a description *about* Tessera, but an actual tree you can walk.
