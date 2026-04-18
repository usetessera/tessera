# API

## Overview
The Acme Vault cloud API. Handles billing (Stripe), user identity lookups
(Supabase), and serves the admin dashboard. Deployed to Railway. Stateless
workers behind a Postgres primary.

## External Systems
- **Stripe**: Billing source of truth. API writes subscription events.
- **Supabase Auth**: JWT validation. API only verifies, never issues.
- **Backblaze B2**: Object storage for user files.

## Actors
- **Desktop App**: Authenticated API calls for sync state, quota, and presigned upload URLs.
- **Mobile**: Authenticated API calls, read-mostly.
- **Admins**: Use the frontend/ Container (admin dashboard).

## Metadata
- **Layer**: Context
- **Tags**: [cloud, backend]
- **Depends on**: None
- **Depended by**: [../desktop-app/architecture.md](../desktop-app/architecture.md), [../mobile/architecture.md](../mobile/architecture.md)
- **Owner**: @api-team
- **Status**: Active

## Key Decisions
- Single Postgres primary; no read replicas yet. Revisit at 10k MAU.
- API is the only product that talks to Stripe. Other systems route billing actions through here.
