# Desktop App

## Overview
The Acme Vault desktop application. An Electron shell that bundles a local
FastAPI backend for filesystem operations and a React renderer for UI. Owns
the user's on-disk vault, sync state, and local-first behavior. Talks to the
cloud API only for auth, quota, and presigned upload URLs.

## External Systems
- **API**: Cloud service for billing, quota, presigned URLs.
- **Backblaze B2**: Direct uploads via presigned URLs from API.
- **Local filesystem**: The authoritative source of vault state.

## Actors
- **End users**: Primary audience. Drag-and-drop, sync, playback.

## Metadata
- **Layer**: Context
- **Tags**: [desktop, electron, offline-first]
- **Depends on**: [../api/architecture.md](../api/architecture.md)
- **Depended by**: [../mobile/architecture.md](../mobile/architecture.md)
- **Owner**: @desktop-team
- **Status**: Active

## Key Decisions
- Offline-first: the desktop app works fully without a cloud connection. Sync is opportunistic.
- Local FastAPI binds to 127.0.0.1 only. Never exposed to the network.
