# Backend

## Overview
HTTP API server. Handles all user-facing CRUD, authentication verification, and enqueues jobs for the worker.

## Technology
- **Runtime**: Node.js 20
- **Framework**: Fastify
- **Data store**: Postgres (shared with worker)

## Metadata
- **Layer**: Container
- **Tags**: api, http
- **Depends on**: [../worker/architecture.md](../worker/architecture.md) _(via shared Postgres job queue)_
- **Depended by**: [../frontend/architecture.md](../frontend/architecture.md)
- **Owner**: @backend-team
- **Status**: Active

## API Surface
- `GET /widgets`, `POST /widgets`, `PATCH /widgets/:id`, `DELETE /widgets/:id`
- `POST /auth/verify` — validates Auth0 token

## Deployment
- Docker container on Fly.io, 3 replicas behind a load balancer.

## Key Decisions
- Uses `src/` as a pass-through folder (no `architecture.md`). All architectural children (api, db, jobs) promote to Component level under Backend.
- Postgres chosen over DynamoDB for strong consistency — see [../adrs/001-chose-postgres.md](../adrs/001-chose-postgres.md).
