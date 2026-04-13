# DB

## Overview
Postgres access layer. Query functions, migration runner, connection pooling.

## Metadata
- **Layer**: Component
- **Tags**: persistence, postgres
- **Depends on**: (external: Postgres)
- **Depended by**: [../api/architecture.md](../api/architecture.md), [../jobs/architecture.md](../jobs/architecture.md), [../../../worker/architecture.md](../../../worker/architecture.md)
- **Owner**: @backend-team
- **Status**: Active

## Interfaces
- `getWidget(id)`, `listWidgets(userId)`, `createWidget(input)`, `updateWidget(id, patch)`, `deleteWidget(id)`
- `enqueueJob(kind, payload)` — writes to the `jobs` table consumed by the worker.

## Key Decisions
- Migrations run at backend boot. See [../../../adrs/001-chose-postgres.md](../../../adrs/001-chose-postgres.md).
