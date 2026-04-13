# Frontend

## Overview
React SPA served as a static bundle. Users sign in via Auth0, then see their widget dashboard.

## Technology
- **Runtime**: Browser (ES2022)
- **Framework**: React 18 + Vite
- **State**: Zustand

## Metadata
- **Layer**: Container
- **Tags**: web, spa
- **Depends on**: [../backend/architecture.md](../backend/architecture.md)
- **Depended by**: (end users)
- **Owner**: @frontend-team
- **Status**: Active

## API Surface
- None — consumes the backend's HTTP API only.

## Deployment
- Static bundle, deployed to Cloudflare Pages. Build on merge to `main`.

## Key Decisions
- Auth0 chosen over homegrown auth for PCI-adjacent compliance posture.
