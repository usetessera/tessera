# Framework Spec Examples

## Overview
Worked examples of the Tessera Framework applied at three scales: a tiny
single-binary service (`tiny-todo-app`), a small SaaS with a handful of
containers (`medium-web-app`), and a monorepo workspace forest with
multiple Landscapes (`monorepo-landscape`). Each example folder is a
self-contained Tessera workspace.

## Files
- `README.md` — Index page introducing the three examples and pointing
  the reader at the right one for their project size.

## Metadata
- **Layer**: Docs
- **Tags**: [examples, spec, framework]
- **Depends on**: [framework-spec](../architecture.md)
- **Depended by**: None
- **Owner**: @jonny
- **Status**: Active

## Key Decisions
- Each example is its own Tessera workspace so a reader can clone it
  standalone and watch the canvas render the spec in action.
- Examples cover the three modes: single-system context, multi-system
  landscape, and the multi-landscape workspace forest from ADR-022.
