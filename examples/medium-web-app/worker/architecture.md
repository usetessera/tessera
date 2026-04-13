# Worker

## Overview
Background job processor. Polls the `jobs` table in Postgres, runs work, calls external services (SendGrid for emails).

## Technology
- **Runtime**: Node.js 20
- **Framework**: None (plain TS loop)
- **Data store**: Postgres (shared with backend)

## Metadata
- **Layer**: Container
- **Tags**: worker, async
- **Depends on**: [../backend/src/db/architecture.md](../backend/src/db/architecture.md) _(shared Postgres schema)_
- **Depended by**: [../backend/architecture.md](../backend/architecture.md) _(backend enqueues jobs for it)_
- **Owner**: @backend-team
- **Status**: Active

## API Surface
- None — no inbound network. Consumes from Postgres, calls outbound services.

## Deployment
- Docker container on Fly.io, single replica (job rows are row-locked on claim).

## Key Decisions
- Postgres `SELECT ... FOR UPDATE SKIP LOCKED` used as the queue mechanism. No separate queue service — see [../adrs/001-chose-postgres.md](../adrs/001-chose-postgres.md).
