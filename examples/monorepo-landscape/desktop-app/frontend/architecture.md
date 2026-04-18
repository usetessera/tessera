# Desktop Renderer

## Overview
The Electron renderer — a React SPA that drives all desktop UI. Talks to the
local backend via HTTP on 127.0.0.1 and to the cloud API for auth + quota.

## Technology
- **Runtime**: Chromium (Electron renderer process)
- **Framework**: React 19 + Vite + Zustand

## Metadata
- **Layer**: Container
- **Tags**: [electron, react]
- **Depends on**: [../backend/architecture.md](../backend/architecture.md)
- **Depended by**: None
- **Owner**: @desktop-team
- **Status**: Active
