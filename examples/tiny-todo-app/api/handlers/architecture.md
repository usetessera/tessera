# Handlers

## Overview
Request handlers for each todo operation. One function per endpoint.

## Files
- `list.go` — `GET /todos` handler
- `create.go` — `POST /todos` handler
- `update.go` — `PATCH /todos/:id` handler
- `delete.go` — `DELETE /todos/:id` handler

## Functions
- `List(w http.ResponseWriter, r *http.Request)` — reads all todos from storage, returns JSON array
- `Create(w http.ResponseWriter, r *http.Request)` — parses JSON body, inserts a todo, returns the created row
- `Update(w http.ResponseWriter, r *http.Request)` — updates fields on an existing todo
- `Delete(w http.ResponseWriter, r *http.Request)` — removes a todo by id

## Metadata
- **Layer**: Module
- **Tags**: http, handlers
- **Depends on**: [../../storage/architecture.md](../../storage/architecture.md)
- **Depended by**: [../architecture.md](../architecture.md)
- **Owner**: @tessera-examples
- **Status**: Active
