# Tessera FAQ

## How is this different from the C4 model?

[C4](https://c4model.com/) inspired Tessera's four-layer hierarchy — the Context / Container / Component / Code model maps almost directly onto Context / Container / Component / Module. The framing is deliberately familiar.

The difference is where the architecture *lives*.

- **C4** is diagram-oriented. You draw the architecture using a tool (Structurizr, Lucidchart, PlantUML, drawio). The diagrams live separately from the code.
- **Tessera** is filesystem-oriented. The architecture *is* the folder tree. The docs live next to the code they describe.

You can absolutely use both. Generate C4 diagrams from a Tessera tree if you want visual output — the semantic model is compatible.

### What Tessera deliberately does *not* cover

C4 is a broader model than Tessera. These are out of scope for the Tessera spec:

- **Dynamic diagrams** (sequence / collaboration). Tessera captures *static* structure. For runtime flows, use Mermaid `sequenceDiagram` or PlantUML and store them alongside the tree — e.g., a `.mermaid.md` file in the relevant Component.
- **Deployment diagrams** (which container runs where, in which region). The L2 Container template has a `## Deployment` prose field; that's intentional. Topology diagrams are output, not input.
- **Code-level class diagrams.** ADR-012 adds optional `## Files` / `## Functions` / `## Classes` sections to the Module template, which covers the 80% case. For UML-scale detail, generate from source with an external tool.

Tessera is a spec for the "what exists and how it connects" layer. Everything above (decisions) lives in ADRs; everything below (runtime, deployment, classes) lives in specialized tools. Tessera's job is to make sure those specialized outputs have a stable tree to point at.

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

For **monorepos that host multiple independent software systems** (e.g., a backend + desktop + mobile + marketing site all in one repo), see the next question — there's a dedicated mode for that shape.

## My monorepo has multiple products in it — how should I model it?

You have two options:

1. **Single-system mode (default).** Treat the whole repo as one system. Each top-level product folder becomes an L2 Container. This works if the products ship together or are best thought of as pieces of one system.
2. **Landscape mode.** Treat the repo as an L0 System Landscape — a workspace of multiple software systems. Each top-level product folder becomes its own L1 Context, with its own Containers and Components underneath. Use this when the products are independently deployed and don't share a runtime.

To enable landscape mode, set `workspace_mode: landscape` in `.tessera/config.yaml` and create a root `architecture.md` using the L0 Landscape template (see [SPEC.md §7.0](./SPEC.md#70-l0-landscape-monorepo-root-landscape-mode-only)). The top-level folders then each need their own `architecture.md` as L1 Contexts.

Landscape mode is opt-in. Existing single-system repos are unaffected by the default. See [ADR-014](../adrs/014-landscape-layer.md) for the rationale.

Example folder structure in landscape mode:

```
my-workspace/                ← L0 Landscape (root architecture.md, config says workspace_mode: landscape)
├── architecture.md          ← describes the landscape as a whole
├── .tessera/config.yaml     ← workspace_mode: landscape
├── api/                     ← L1 Context (a software system)
│   ├── architecture.md
│   ├── backend/             ← L2 Container
│   └── web/                 ← L2 Container
├── desktop-app/             ← L1 Context (a software system)
│   ├── architecture.md
│   └── ...
└── mobile/                  ← L1 Context (a software system)
    ├── architecture.md
    └── ...
```

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

A partial tree is better than no tree. A 40%-covered repo gives you 40% of the value; a 0%-covered repo gives you nothing. Don't treat adoption as a big-bang documentation sprint — it will stall. Use the five-phase playbook below.

### Phase 1 — Context only (1–2 hours)

Write one file: `architecture.md` at the repo root, using the L1 Context template. Cover:

- What the system is and the problem it solves (Overview).
- External systems it talks to (payment processors, third-party APIs, auth providers).
- Actors who use it (end users, admins, other services).
- Top-level `Key Decisions` — three to five, no more.

Do not descend further yet. Commit. Even the Context page alone forces a useful conversation.

### Phase 2 — Containers (half a day)

Identify your deployable or distinct units. These usually map to top-level folders already: `backend/`, `frontend/`, `worker/`, `mobile/`, `cli/`. For each, add an `architecture.md` using the L2 Container template. Cover:

- Runtime and framework.
- Public API surface (REST endpoints, CLI commands, npm exports — whatever the container exposes).
- Deployment (where it runs, how it's packaged).
- Dependencies on other containers.

Update the root `architecture.md` to reference each Container in its `Key Decisions` or by linking.

**Don't create Containers that don't correspond to real deployables.** If `src/`, `lib/`, and `internal/` are technical scaffolding, skip them — the pass-through rule handles them automatically.

### Phase 3 — Components, opportunistically (ongoing)

From here, don't block the team. Add `architecture.md` files to inner folders only as you touch them:

- When you open a folder to change its code, add or update its `architecture.md` before you finish the PR.
- When you create a new folder for a logical grouping, write its `architecture.md` as part of the same commit.
- When you onboard someone, have them add an `architecture.md` to the first Component they work on — it's the fastest way to build mental model.

Aim for 40% Component coverage in month one, 80% in month three. Don't chase 100% — the long tail is usually folders that are about to be deleted or merged anyway.

### Phase 4 — Modules and file-level detail (as needed)

Modules (L4) are leaves. Add them when you want AI agents to reason about specific files. The L4 template's optional `## Files` / `## Functions` / `## Classes` sections (per ADR-012) make Modules the unit AI tooling can diff against code to detect drift.

You don't need every Module documented. Start with:

- Modules that are hot paths in code review.
- Modules that new team members repeatedly get confused by.
- Modules that AI agents keep miscategorizing.

### Phase 5 — Dependency graph (after the tree is stable)

Only fill in `Depends on` / `Depended by` after the tree has settled. Doing this before the tree is stable means re-editing links every time a folder moves.

When you do fill them in, do it in one sitting per Container: walk the tree, note the imports, write bilateral links. Commit the whole Container's graph in one PR so reviewers can sanity-check it. Then wire up a pre-commit or CI check (see [SPEC.md §8.1](./SPEC.md#81-keeping-the-graph-consistent)) so it stays consistent.

### Using tooling to accelerate adoption

If you use the Tessera MCP server, `scaffold_existing_codebase` proposes a full draft of Phases 2–3 in one pass: it walks your repo, applies the pass-through rule, suggests a layer for each folder, and returns a list of proposed `architecture.md` files. Review the proposal, edit as needed, then apply. You still do Phase 1 (the Context prose) by hand — the tool can't write your system's intent for you. But it collapses days of mechanical work into minutes.

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
