# Contributing to Tessera

Tessera is an open specification. Contributions are welcome and shape the spec over time.

## What counts as a contribution

- **Clarifications** — sections of the spec that are ambiguous or contradict themselves.
- **New examples** — real-world trees at scales we don't yet cover (monorepos, microservice fleets, polyglot stacks).
- **FAQ entries** — questions you had adopting the spec that aren't answered yet.
- **Spec evolution** — edge cases the current rules don't handle cleanly. These are the most valuable contributions.

## How to propose a change

1. **Open an issue first** for spec changes. Describe the gap or ambiguity you hit. Discussion before PR saves everyone time.
2. **For typos, clarifications, or new examples**, a PR directly is fine.
3. **For changes to the layer rules, templates, or maintenance rules**, expect more discussion. These are the load-bearing parts of the spec.

## Versioning

The spec follows semantic versioning. The current version is declared in [SPEC.md §13](./SPEC.md#13-versioning).

- **Patch** (1.0.x) — clarifications that don't change meaning.
- **Minor** (1.x.0) — additions (new optional metadata fields, new templates) that are backwards compatible.
- **Major** (x.0.0) — breaking changes to layer rules or mandatory structure.

## Code of conduct

Be kind. Assume good faith. The point of the spec is to help people document their systems — discussions should reflect that spirit.

## License

Contributions are accepted under the same license as the spec: Creative Commons Attribution 4.0 International.
