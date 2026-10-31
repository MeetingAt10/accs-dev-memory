# ACCS developer context index

- `accs-system`: React 19 + Vite 7 frontend, Express 5 backend on Lambda; dev stack `accs-dev` on CloudFront, synthetic data, no database or AI provider. [Current state](architecture/current-state.md)
- App split into seven DOC003 module sites (Home, Forums, Schedule, Cases, Evaluations, Records, Admin) with one shared sign-in. [Current state](architecture/current-state.md#module-sites-2026-11-01)
- `accs-landing` is a separate Next.js scaffold; its Figma notes are in `BRAND_REFERENCE.md`. [Current state](architecture/current-state.md)
- Memory vaults are source-linked Markdown, with later retrieval tooling gated by measured need. Project decision: `accs-memory/decisions/0001-memory-architecture.md`.
- Before coding, inspect the current repository and its local instructions; this index may lag implementation.
