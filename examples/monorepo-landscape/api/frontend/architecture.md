# API Admin Dashboard

## Overview
Internal-only admin dashboard for billing support and user lookups. Consumes
the API's `/admin/*` endpoints. Served from Railway static hosting.

## Technology
- **Runtime**: Static SPA
- **Framework**: React 19 + Vite

## Metadata
- **Layer**: Container
- **Tags**: [react, admin]
- **Depends on**: [../backend/architecture.md](../backend/architecture.md)
- **Depended by**: None
- **Owner**: @api-team
- **Status**: Active

## Key Decisions
- Admin-only; gated by a Supabase role claim. Not in Google search index.
