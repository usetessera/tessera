# Todo App

## Overview
A minimal single-user todo application. Stores todos locally in a SQLite file. Exposes an HTTP API.

## External Systems
- **SQLite**: Local embedded database file. No network dependency.

## Actors
- **End user**: Creates, reads, updates, and deletes todos via the HTTP API.

## Metadata
- **Layer**: Context
- **Tags**: example, demo
- **Owner**: @tessera-examples
- **Status**: Active

## Key Decisions
- Single-binary Go service chosen for simplicity. See `adrs/001-single-binary.md` (not included in this example).
- No auth — this is a demo app.
