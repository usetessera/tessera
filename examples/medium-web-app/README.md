# Medium example — Multi-container web app

A more realistic Tessera tree: frontend SPA + backend API + background worker, with a Postgres database as an external system.

## What this demonstrates

- **Three containers** — separate deployables (frontend, backend, worker).
- **The pass-through rule** — `backend/src/` has no `architecture.md`, so its children promote up.
- **Dependencies** — `backend/api` depends on `backend/db`, and `worker` depends on `backend/db`.
- **ADRs** — `adrs/` folder at root, referenced from relevant elements.

## Structure

```
medium-web-app/
├── architecture.md                   ← L1 Context
├── adrs/
│   └── 001-chose-postgres.md
├── frontend/
│   ├── architecture.md               ← L2 Container
│   ├── pages/
│   │   └── architecture.md           ← L3 Component
│   └── components/
│       └── architecture.md           ← L3 Component
├── backend/
│   ├── architecture.md               ← L2 Container
│   └── src/                          ← (pass-through, no architecture.md)
│       ├── api/
│       │   └── architecture.md       ← L3 Component
│       ├── db/
│       │   └── architecture.md       ← L3 Component
│       └── jobs/
│           └── architecture.md       ← L3 Component
└── worker/
    └── architecture.md               ← L2 Container
```
