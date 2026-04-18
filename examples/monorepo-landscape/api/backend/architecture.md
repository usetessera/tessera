# API Backend

## Overview
The FastAPI service that serves HTTP endpoints for billing, identity lookup,
and storage URL minting. Deployed as a single container image on Railway.

## Technology
- **Runtime**: Python 3.12
- **Framework**: FastAPI
- **Data store**: Postgres (shared with worker jobs), Redis (rate limiting, session cache)

## Metadata
- **Layer**: Container
- **Tags**: [python, fastapi]
- **Depends on**: None
- **Depended by**: [../frontend/architecture.md](../frontend/architecture.md)
- **Owner**: @api-team
- **Status**: Active

## API Surface
- `POST /billing/*` — Stripe webhook + subscription management
- `GET /storage/presigned` — Mint B2 upload URLs for authenticated clients
- `GET /me` — Current user profile

## Deployment
Railway. Auto-deploys from `main` via GitHub Actions. Zero-downtime via Railway's rolling deploy.

## Key Decisions
- No background queue yet. Stripe webhooks run inline on the request thread. Move to worker jobs when P95 > 2s.
