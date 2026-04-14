# Tessera

> An architecture documentation framework where **the folder structure IS the architecture**.

Every folder in your codebase is an architectural element. Every folder contains an `architecture.md` describing what it is, what depends on it, and why. Your architecture lives beside your code, in git, updated in the same pull request as the change itself. It can never drift.

Tessera is a **free, open specification** — like the [C4 model](https://c4model.com/). You don't need special tools to adopt it. A text editor is enough. Optional tooling (an [MCP server](https://github.com/usetessera) and a [VS Code extension](https://github.com/usetessera)) exists to make the workflow delightful, but the framework stands on its own. (The MCP and VS Code extension are still under development)

## Why

Most architecture documentation has the same failure mode: it lives somewhere else. A Confluence page, a Miro board, a Lucidchart diagram, a Structurizr DSL file. The code ships, the doc doesn't get updated, six months later the doc is a lie.

Tessera fixes this by putting the docs *in the tree, next to the code they describe*. There's nowhere else for them to live. When you rename a folder, you rename the element. When you delete a folder, you delete the element. When you add a function, the sibling `architecture.md` is right there, begging to be updated.

## The 60-second tour

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

Four layers, inspired by C4:

| Layer | What it is | Example |
|---|---|---|
| **L1 Context** | The whole system and its outside world | Your app, its users, its external services |
| **L2 Container** | Deployable/distinct units | `backend/`, `frontend/`, `worker/` |
| **L3 Component** | Logical groupings inside a container | `api/`, `db/`, `jobs/` |
| **L4 Module** | Leaf — only files, no subfolders | `auth/`, `users/`, `billing/` |

That's the whole idea. Everything else is details on how to make it work cleanly.

## Quick start

1. Put an `architecture.md` at your repo root using the **Context** template from [`SPEC.md`](./SPEC.md).
2. For each top-level folder that represents a distinct deployable unit, add an `architecture.md` using the **Container** template.
3. Drill down. Add Components and Modules as the tree demands.
4. Commit. You're done.

When you change code, update the sibling `architecture.md` in the same commit. That's the entire discipline.

## What's in this repo

- **[`SPEC.md`](./SPEC.md)** — the full specification: layer rules, templates, edge cases
- **[`FAQ.md`](./FAQ.md)** — how Tessera relates to C4, ADRs, Structurizr, and why it exists
- **[`examples/`](./examples/)** — worked examples at different scales
- **[`LICENSE`](./LICENSE)** — Creative Commons Attribution 4.0. Use this however you want; just credit the source.

## Tooling (optional)

You can adopt Tessera with nothing but a text editor. If you want more:

- **Tessera MCP server** (`@tessera/mcp`) — lets AI coding agents (Claude Code, Cursor, etc.) read and maintain your architecture tree automatically. Free, open source, MIT.
- **Tessera VS Code extension** — interactive canvas over your architecture tree. Free tier for read/navigate; Pro tier for editing, diagrams, and export.

Both are separate projects and strictly optional. The spec is the product. They are also still under development. To be released soon

## Contributing

Found a gap in the spec? Have a pattern Tessera doesn't handle well? Open an issue. The spec improves by being used.

## License

Creative Commons Attribution 4.0 International. See [LICENSE](./LICENSE).
