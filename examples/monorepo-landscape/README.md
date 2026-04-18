# Example: Monorepo Landscape

A minimal example of a Tessera tree in **landscape mode** — a monorepo that hosts multiple independently-deployable software systems.

## Shape

```
monorepo-landscape/          L0 Landscape (architecture.md + .tessera/config.yaml with workspace_mode: landscape)
├── api/                     L1 Context — the cloud API product
│   ├── architecture.md
│   ├── backend/             L2 Container
│   │   └── architecture.md
│   └── frontend/            L2 Container (admin dashboard)
│       └── architecture.md
├── desktop-app/             L1 Context — the desktop product
│   ├── architecture.md
│   ├── backend/             L2 Container (bundled local FastAPI)
│   │   └── architecture.md
│   └── frontend/            L2 Container (Electron renderer)
│       └── architecture.md
└── mobile/                  L1 Context — the mobile product
    └── architecture.md
```

## What this demonstrates

- A root `architecture.md` using the L0 **Landscape** template. It describes the workspace as a whole: what systems live here, what binds them together, what external actors interact with them.
- `.tessera/config.yaml` with `workspace_mode: landscape`. This is the flag that tells Tessera tools to treat the root as L0 instead of L1.
- Three top-level Contexts (`api/`, `desktop-app/`, `mobile/`), each its own software system with its own Containers.
- Cross-system dependency relationships documented in the metadata (e.g., the desktop app depends on the API).

## When to use this shape

Use landscape mode when the top-level folders of your repo are *distinct software systems* that ship independently — not subsystems of one larger product. If the top-level folders are just the backend and frontend of one product that always ships together, stay in the default context mode.

See [ADR-014](../../../adrs/014-landscape-layer.md) for the decision record, and [SPEC.md §2.2](../../SPEC.md#22-workspace-modes-context-vs-landscape) for the full rules.
