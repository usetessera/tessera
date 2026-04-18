# Acme Vault Workspace

## Overview
A monorepo that hosts the three customer-facing software systems for Acme
Vault: a cloud API that owns billing and shared storage, a desktop application
that holds the user's files, and a mobile companion app for on-the-go access.
Systems ship on independent release cycles and own their own data boundaries;
they share the Acme Vault identity (Supabase JWT) and the Acme Vault storage
bucket conventions, but no code.

## Systems
- **API**: Cloud-hosted FastAPI. Owns billing, shared storage routing, and user identity.
- **Desktop App**: Electron + local FastAPI. Owns the user's on-disk vault and sync state.
- **Mobile**: React Native companion. Read-mostly client; browses and plays back content synced by the desktop app.

## External Systems
- **Supabase Auth**: Shared identity provider. All systems accept Supabase JWTs.
- **Backblaze B2**: Shared object storage. API writes under user-scoped prefixes; desktop app uploads directly via presigned URLs.
- **Stripe**: Billing. API is the only system that talks to Stripe directly.

## Actors
- **End users**: Interact with Desktop App and Mobile. Never call API directly.
- **Admins**: Use the API's admin dashboard (API's frontend Container) for billing and support tickets.

## Metadata
- **Layer**: Landscape
- **Tags**: [monorepo, multi-product]
- **Owner**: @platform-team
- **Status**: Active

## Key Decisions
- Multi-product monorepo; each product releases independently. See ADR-014 (landscape layer) for how this is modeled.
- Shared auth via Supabase JWTs; no custom identity service per product.
- No shared code packages at the repo root — each Context is self-contained.
