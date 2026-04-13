# Storage

## Overview
SQLite-backed persistence layer for todos. Opens the database on startup, provides typed CRUD functions.

## Files
- `storage.go` — connection setup, schema migration
- `todos.go` — CRUD operations

## Functions
- `Open(path string) (*DB, error)` — opens/creates the SQLite file, runs migrations
- `ListTodos(ctx) ([]Todo, error)` — returns all todos
- `CreateTodo(ctx, text string) (Todo, error)` — inserts a new todo
- `UpdateTodo(ctx, id int, patch TodoPatch) (Todo, error)` — updates fields
- `DeleteTodo(ctx, id int) error` — removes a todo

## Classes
- `DB` — wraps `*sql.DB` with the todo-specific query methods
- `Todo` — the todo row: `{id, text, done, created_at}`

## Metadata
- **Layer**: Module
- **Tags**: persistence, sqlite
- **Depends on**: (none — SQLite is embedded)
- **Depended by**: [../api/handlers/architecture.md](../api/handlers/architecture.md)
- **Owner**: @tessera-examples
- **Status**: Active

## Key Decisions
- SQLite chosen over Postgres because this is a single-user local app — zero operational cost.
