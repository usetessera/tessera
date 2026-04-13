# API

## Overview
HTTP route handlers for the backend. Maps Fastify routes to business logic, handles request validation.

## Metadata
- **Layer**: Component
- **Tags**: http, routes
- **Depends on**: [../db/architecture.md](../db/architecture.md), [../jobs/architecture.md](../jobs/architecture.md)
- **Depended by**: [../../architecture.md](../../architecture.md)
- **Owner**: @backend-team
- **Status**: Active

## Interfaces
- `registerRoutes(app: FastifyInstance): void` — attaches all routes
- Exposes the HTTP endpoints listed in the Backend container's API surface.
