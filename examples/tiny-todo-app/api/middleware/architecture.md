# Middleware

## Overview
Cross-cutting HTTP concerns: request logging and JSON content-type enforcement.

## Files
- `logger.go` — request/response logging
- `json.go` — sets `Content-Type: application/json` on responses

## Functions
- `Logger(next http.Handler) http.Handler` — wraps a handler with structured access logging
- `JSON(next http.Handler) http.Handler` — sets response content type

## Metadata
- **Layer**: Module
- **Tags**: http, middleware
- **Depends on**: (none)
- **Depended by**: [../architecture.md](../architecture.md)
- **Owner**: @tessera-examples
- **Status**: Active
