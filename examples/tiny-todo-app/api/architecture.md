# API

## Overview
HTTP interface to the todo app. Accepts JSON requests, dispatches to handlers, returns JSON responses.

## Metadata
- **Layer**: Component
- **Tags**: http, api
- **Depends on**: [../storage/architecture.md](../storage/architecture.md)
- **Depended by**: (root — entry point)
- **Owner**: @tessera-examples
- **Status**: Active

## Interfaces
- `GET /todos` — list todos
- `POST /todos` — create a todo
- `PATCH /todos/:id` — update a todo
- `DELETE /todos/:id` — delete a todo

## Key Decisions
- Chi router chosen for its minimal surface area.
