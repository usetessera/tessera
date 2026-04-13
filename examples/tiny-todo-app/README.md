# Tiny example — Todo app

A minimal Tessera tree for a single-binary todo app. Illustrates the core concepts without any complexity.

## Structure

```
tiny-todo-app/
├── architecture.md      ← L1 Context
├── api/                 ← L2 Container (well, since this is a single-container app,
│   ├── architecture.md     the root IS the Container. See note below.)
│   ├── handlers/
│   │   └── architecture.md  ← L4 Module
│   └── middleware/
│       └── architecture.md  ← L4 Module
└── storage/
    └── architecture.md  ← L4 Module
```

## Note on single-container apps

For apps with only one deployable unit, you can either:

1. Treat the repo root as Context *and* Container (pragmatic — this example).
2. Put the whole app inside a single top-level folder like `app/` (more formally correct).

Both are acceptable. Option 1 is simpler; option 2 is clearer if the app might grow into a multi-container system later.
