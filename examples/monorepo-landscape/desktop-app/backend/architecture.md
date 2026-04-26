# Desktop Backend

## Overview
A local FastAPI process bundled inside the Electron app. Handles all
filesystem operations, local document metadata, and per-file sync state. Binds
to 127.0.0.1 and is never exposed to the network.

## Technology
- **Runtime**: Python 3.12 (bundled via PyInstaller)
- **Framework**: FastAPI
- **Data store**: Local JSON sidecars + SQLite (per-library metadata)

## Metadata
- **Layer**: Container
- **Tags**: [python, local, offline]
- **Depends on**: None
- **Depended by**: [../frontend/architecture.md](../frontend/architecture.md)
- **Owner**: @desktop-team
- **Status**: Active

## Deployment
Bundled into the Electron app installer. No separate deploy — ships with the desktop app release.
