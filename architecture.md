# Framework Spec

## Overview
The free, open Tessera Framework specification. Defines the convention
("every architectural element is a folder with `architecture.md`"), the
extended C4 layer model (Landscape, Context, Container, Component,
Module), the pass-through rule, the uniform-children rule, the
multi-landscape workspace mode, and the example workspaces that show the
spec applied at different scales.

This folder is the standalone artifact published to the public framework
repo. The spec is text-only; nothing here imports the extension or the
MCP server, and nothing here ships in any compiled output.

## Files
- `SPEC.md` — Normative specification. The single source of truth for
  what a Tessera-compliant workspace looks like.
- `README.md` — Marketing-friendly entry point for the public repo.
- `FAQ.md` — Frequently asked questions about the framework.
- `CONTRIBUTING.md` — Contribution guide for the spec.
- `LICENSE` — Creative Commons license (the spec itself is CC, separate
  from the MIT-licensed MCP server and the closed-source extension).

## Metadata
- **Layer**: Docs
- **Tags**: [spec, framework, public, creative-commons]
- **Depends on**: None
- **Depended by**: [examples](./examples/architecture.md)
- **Owner**: @jonny
- **Status**: Active

## Key Decisions
- Spec is its own deliverable, distinct from the MCP server and the
  extension (see ADR-010).
- Examples live under `examples/` and are part of the spec — they are
  the executable test cases for "does this feel right at this scale?"
