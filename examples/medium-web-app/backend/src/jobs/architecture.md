# Jobs

## Overview
Typed job-enqueuing helpers. The backend calls these when a user action should trigger background work (e.g., send digest email).

## Metadata
- **Layer**: Component
- **Tags**: jobs, queue
- **Depends on**: [../db/architecture.md](../db/architecture.md)
- **Depended by**: [../api/architecture.md](../api/architecture.md)
- **Owner**: @backend-team
- **Status**: Active

## Interfaces
- `enqueueDigestEmail(userId: string): Promise<void>`
- `enqueueInventorySync(widgetId: string): Promise<void>`
