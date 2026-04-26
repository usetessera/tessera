# Tessera

> An architecture documentation framework where **the folder structure IS the architecture**.

Every folder in your codebase is an architectural element. Every folder contains an `architecture.md` describing what it is, what depends on it, and why. Your architecture lives beside your code, in git, updated in the same pull request as the change itself. It can never drift.

Tessera is a **free, open specification** — like the [C4 model](https://c4model.com/). You don't need special tools to adopt it. A text editor is enough. Optional tooling (an [MCP server](https://github.com/usetessera/tessera-mcp) and a [VS Code extension](https://github.com/usetessera)) exists to make the workflow delightful, but the framework stands on its own.

## Why

Most architecture documentation has the same failure mode: it lives somewhere else. A Confluence page, a Miro board, a Lucidchart diagram, a Structurizr DSL file. The code ships, the doc doesn't get updated, six months later the doc is a lie.

Tessera fixes this by putting the docs *in the tree, next to the code they describe*. There's nowhere else for them to live. When you rename a folder, you rename the element. When you delete a folder, you delete the element. When you add a function, the sibling `architecture.md` is right there, begging to be updated.

## The 60-second tour

For a normal single-system repository, the root is an L1 Context:

```
my-app/
├── architecture.md          ← L1 Context (whole system)
├── backend/
│   ├── architecture.md      ← L2 Container
│   ├── api/
│   │   ├── architecture.md  ← L3 Component
│   │   ├── auth/
│   │   │   ├── architecture.md  ← L4 Module
│   │   │   ├── login.ts
│   │   │   └── logout.ts
│   │   └── users/
│   │       ├── architecture.md  ← L4 Module
│   │       └── ...
│   └── db/
│       └── ...
└── frontend/
    └── ...
```

For a multi-system monorepo, enable `workspace_mode: landscape` and the root becomes an L0 Landscape:

```
workspace/
├── .tessera/
│   └── config.yaml          ← workspace_mode: landscape
├── architecture.md          ← L0 Landscape (all systems)
├── api/
│   └── architecture.md      ← L1 Context (one software system)
├── desktop-app/
│   └── architecture.md      ← L1 Context (another software system)
└── mobile/
    └── architecture.md      ← L1 Context (another software system)
```

Four layers by default, with an optional L0 Landscape for monorepos:

| Layer | What it is | Example |
|---|---|---|
| **L0 Landscape** | Optional monorepo/workspace of multiple systems | repo root in `landscape` mode |
| **L1 Context** | The whole system and its outside world | Your app, its users, its external services |
| **L2 Container** | Deployable/distinct units | `backend/`, `frontend/`, `worker/` |
| **L3 Component** | Logical groupings inside a container | `api/`, `db/`, `jobs/` |
| **L4 Module** | Leaf — only files, no subfolders | `auth/`, `users/`, `billing/` |

That's the whole idea. Everything else is details on how to make it work cleanly.

## Quick start

1. Pick a workspace mode:
   - Use the default **context mode** for one software system.
   - Use **landscape mode** for a monorepo with multiple independent systems.
2. Put an `architecture.md` at your root:
   - Context mode: use the **Context** template.
   - Landscape mode: set `workspace_mode: landscape` in `.tessera/config.yaml` and use the **Landscape** template.
3. Add child elements:
   - Context mode: top-level deployable folders use the **Container** template.
   - Landscape mode: top-level system folders use the **Context** template.
4. Drill down. Add Containers, Components, and Modules as the tree demands.
5. Commit. You're done.

When you change code, update the sibling `architecture.md` in the same commit. That's the entire discipline.

## What's in this repo

- **[`SPEC.md`](./SPEC.md)** — the full v1.1 specification: layer rules, templates, edge cases
- **[`FAQ.md`](./FAQ.md)** — how Tessera relates to C4, ADRs, Structurizr, and why it exists
- **[`examples/`](./examples/)** — worked examples at different scales
- **[`LICENSE`](./LICENSE)** — Creative Commons Attribution 4.0. Use this however you want; just credit the source.

## Tooling (optional)

You can adopt Tessera with nothing but a text editor. If you want more:

- **Tessera MCP server** (`@tessera/mcp`) — lets AI coding agents (Claude Code, Cursor, etc.) read and maintain your architecture tree automatically. Free, open source, MIT.
- **Tessera VS Code extension** — interactive canvas over your architecture tree. Early Access builds currently unlock all features while the workflow is refined.

Both are separate projects and strictly optional. The spec is the product.

## Contributing

Found a gap in the spec? Have a pattern Tessera doesn't handle well? Open an issue. The spec improves by being used.

## License

Creative Commons Attribution 4.0 International. See [LICENSE](./LICENSE).
