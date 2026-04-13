# Tessera FAQ

## How is this different from the C4 model?

[C4](https://c4model.com/) inspired Tessera's four-layer hierarchy — the Context / Container / Component / Code model maps almost directly onto Context / Container / Component / Module. The framing is deliberately familiar.

The difference is where the architecture *lives*.

- **C4** is diagram-oriented. You draw the architecture using a tool (Structurizr, Lucidchart, PlantUML, drawio). The diagrams live separately from the code.
- **Tessera** is filesystem-oriented. The architecture *is* the folder tree. The docs live next to the code they describe.

You can absolutely use both. Generate C4 diagrams from a Tessera tree if you want visual output — the semantic model is compatible.

## How is this different from ADRs?

They solve different problems.

- **ADRs** record *decisions* — why we chose PostgreSQL over DynamoDB in March 2024.
- **Tessera** records *current state* — what the system looks like today.

ADRs are immutable historical artifacts. `architecture.md` files are living documents. Use both: ADRs for the *why* that outlasts people, Tessera for the *what* that changes as the code changes. Tessera has a built-in ADR convention (see [SPEC.md §10](./SPEC.md#10-decision-records-adrs)) so they stay connected.

## How is this different from Structurizr / arc42 / [tool X]?

Most architecture tools treat the architecture as a **separate artifact** you maintain alongside the code. That creates a maintenance problem: two things to update, and usually only one gets updated.

Tessera treats the architecture as an **emergent property** of the code's organization. You don't maintain a separate model; you maintain the folder tree and its `architecture.md` files, and the model falls out of that.

This means:
- No import/export round-trips.
- No "who has the latest version of the diagram?"
- Refactoring code *is* refactoring architecture.
- Git history is architecture history.

## Do I need the MCP server or VS Code extension to use Tessera?

**No.** The framework is a free, open specification. A text editor is sufficient. The tools are for convenience — AI-agent integration and visual canvas — but they're strictly optional.

## Does this work for [language / framework / monorepo / monolith / microservices]?

Tessera is language-agnostic and architecture-style-agnostic. If your project has folders, it works. The framework makes no assumptions about:

- Programming language
- Runtime or deployment model
- Monolith vs. microservices vs. modular monolith
- Package manager or build system

## My repo has `src/`, `lib/`, `pkg/`, `internal/` — how do those fit?

They don't need to. Those are language/framework scaffolding folders, not architectural elements. Don't put `architecture.md` in them. Under the **pass-through rule** ([SPEC.md §4](./SPEC.md#4-the-pass-through-rule)), a folder without an `architecture.md` isn't an architectural element, and its children are promoted to the parent's layer.

```
backend/                    ← Container (has architecture.md)
├── architecture.md
└── src/                    ← NOT an element (no architecture.md)
    ├── api/                ← Component
    │   └── architecture.md
    └── db/                 ← Component
        └── architecture.md
```

## What if my project has more than four levels deep?

Four is the number of *architectural* layers, not folder-tree depth. Your repo can have arbitrary depth; pass-through folders (§4) absorb the excess. At the architectural layer, you still have at most Context → Container → Component → Module.

If you find yourself wanting five architectural layers, it's almost always a sign that a Container should be split into multiple Containers, or that some Components should be sub-Components.

## What if I'm adopting this in an existing large repo?

Start at the top and expand incrementally:

1. **Day 1:** Add `architecture.md` at the repo root only (Context layer). Don't try to fill in everything. Even the Context page alone is valuable.
2. **Week 1:** Add `architecture.md` to each top-level deployable folder (Container layer).
3. **As you work:** When you open a folder to change something, add or update its `architecture.md` before leaving. Progress comes from the flow of normal work, not a big-bang documentation sprint.

A partial tree is better than no tree. A 40%-covered repo gives you 40% of the value; a 0%-covered repo gives you nothing.

## How do I handle generated code or vendored dependencies?

Don't document them. Add them to your ignore list (`.tessera/config.yaml` if using tooling, or just skip writing `architecture.md` files there — the pass-through rule handles the rest). Architecture describes *your* code, not third-party code.

## What if two elements truly depend on each other circularly?

Write the dependency in both directions ("Depends on" on both sides). Tessera won't prevent you from expressing a cycle — it just surfaces them. Whether cycles are acceptable is a design question for your team, not a framework rule.

## Can I customize the templates?

Yes. The templates in [SPEC.md](./SPEC.md#7-architecturemd-templates) are the minimum agreed-upon format. Add sections your team needs (SLAs, on-call contacts, runbook links, etc.). Just keep the required sections — tooling that reads Tessera trees depends on them.

## Is this a standard? Who maintains it?

Tessera is an open specification published under Creative Commons Attribution 4.0. It's not (yet) a formal standard — it's a convention documented here. The spec is versioned; see [SPEC.md §13](./SPEC.md#13-versioning).

Contributions, issues, and real-world adoption feedback are welcome.

## Why the name "Tessera"?

A *tessera* is a single tile in a mosaic. Each `architecture.md` is a tile; the whole system is the mosaic. You never see the whole picture by looking at one tile, but every tile contributes to it.
