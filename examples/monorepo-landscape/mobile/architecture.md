# Mobile

## Overview
The Acme Vault mobile companion app. Read-mostly client that browses and
plays back content synced by the desktop app. Authenticates against the cloud
API and streams media via presigned B2 URLs.

## External Systems
- **API**: Auth, quota, presigned download URLs.

## Actors
- **End users**: On-the-go browsing and playback.

## Metadata
- **Layer**: Context
- **Tags**: [mobile, react-native]
- **Depends on**: [../api/architecture.md](../api/architecture.md)
- **Depended by**: None
- **Owner**: @mobile-team
- **Status**: Active

## Key Decisions
- No write path yet — MVP is read-only. Upload flow deferred to v2.
