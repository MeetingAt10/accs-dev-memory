# ACCS developer context index

- `accs-frontend` (React 19 + Vite) and `accs-backend` (Express 5 on Lambda); `dev` deploys to `accs-dev`, `main` to `accs-prod`. [Current state](architecture/current-state.md)
- Synthetic data now persists: domain data in the DOC006 DynamoDB tables, accounts in an S3 auth store, sessions and reset codes in `ccs_identity`. [Current state](architecture/current-state.md#data)
- One portal host serves all seven module sites as navigation groups (since 2026-11-05). [Current state](architecture/current-state.md#hosting-one-portal-host-since-2026-11-05)
- Still missing: a real AI provider, SES email, and invite delivery. [Current state](architecture/current-state.md#not-yet-built-or-configured)
- `accs-landing` is a separate Next.js site; its Figma notes are in `BRAND_REFERENCE.md`.
- Memory vaults are source-linked Markdown, with later retrieval tooling gated by measured need. Project decision: `accs-memory/decisions/0001-memory-architecture.md`.
- Before coding, inspect the current repository and its local instructions; this index may lag implementation.
