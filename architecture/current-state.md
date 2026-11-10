# Current implementation state

Status: Observed; not a completed-feature list.
Last reviewed: 2026-11-10.

## Repositories

The app lives in two repositories in the MeetingAt10 organization (it was `accs-system/frontend` and `accs-system/backend` until early November):

- `accs-frontend`: React 19, Vite, TypeScript, TanStack Router and Query, on `@meetingat10/design-system` v1.0.1.
- `accs-backend`: Express 5 and TypeScript on Node 22, run on AWS Lambda through `serverless-http`. Zod validates every request and response.

Infrastructure is in `accs-architecture` (CloudFormation, ADRs), and page specs are in `accs-docs`. Both app repositories deploy `dev` to the `accs-dev` stack and `main` to `accs-prod` through GitHub Actions. A CI check rejects any commit with a `Co-Authored-By: Claude` trailer.

## Hosting (one portal host, since 2026-11-05)

| Site | Prod | Dev |
|---|---|---|
| Landing | `access-ccs.com` | `dev.access-ccs.com` |
| Portal (every signed-in module) | `portal.access-ccs.com` | `portal-dev.access-ccs.com` |
| Design system | `design.access-ccs.com` | `design-dev.access-ccs.com` |
| Meeting At 10 | `team.access-ccs.com` | `team-dev.access-ccs.com` |

The seven module sites (Home, Forums, Schedule, Cases, Evaluations, Records, Admin) are navigation groups on the portal, not separate hosts. Each stack has its own HTTP API, Lambda, DynamoDB tables and auth store. The backend's `CORS_ORIGIN` lists only the portal origin.

## Data

The data is still synthetic seed data (sample students, faculty, reports), but it now persists:

- **Domain data:** 21 collections in the twelve DOC006 DynamoDB tables (`<TABLE_PREFIX>ccs_*`, ADR 0004), one item per record with a version check on every write. Audit entries are add-only. See `accs-backend` README, "Storage boundary".
- **Accounts and credentials:** one JSON object in the auth store S3 bucket (`AUTH_STORE_BUCKET`), written with ETag checks and retried up to five times, then 409.
- **Sessions, reset codes, failed-attempt counters:** `SESSION`, `RESET` and `THROTTLE` items in `ccs_identity`, with TTL on `expires_epoch`.
- `users`, `spaces`, `organizations` and `offenses` still come from the seed per instance (users are overlaid from the auth store).

## Auth and accounts

- No public registration. The Dean activates the secretary; the secretary creates faculty, advisers, chairs and students (manually or from the MIS Excel list). First sign-in forces a new username and password.
- Sessions: 30 min idle / 12 h, or 7 days idle / 30 days with "keep me signed in" (approved under D-08 on 2026-11-10).
- Password reset: 6-digit code, 15 minutes, 5 guesses, 5 codes per hour, 60-second cooldown (approved). With `MAIL_FROM` set, SES emails the code; otherwise local runs log it and production leaves resets to an administrator. SES is not configured on either stack yet.
- 5 failed attempts in 15 minutes per client and identifier, shared across Lambda instances.

## Not yet built or configured

- AI assistant: `demoAssistant` is a deterministic stub (accs-backend issue #8). Provider, model and data scope are undecided.
- Invite emails (temporary passwords) are not sent; the UI shows them once to pass on.
- Account limits: the Lambda timeout is 10 s, and the AWS account's limit of 10 concurrent Lambda executions is shared by accs-dev and accs-prod.

## Checks

- Backend: `npm test` (Vitest, about 1080 tests), `npm run typecheck`, `npm run build:lambda`. Run Vitest on Node 22 locally; it can crash with `Channel closed` on Node 24.
- `tests/doc003-modules.test.ts` checks that every DOC003 module's owning role reaches its pages. `tests/doc-traceability.test.ts` covers DOC005 case IDs by name.

Sources: `accs-backend` README and `src/` (dev at 2026-11-10), `accs-architecture` README and `adr/0004-dynamodb-domain-tables.md`, `accs-docs/deltas/2026-11-05-*.md`. Recheck code before relying on this snapshot.

## History

- 2026-11-10: persistence, auth hardening, one portal host and the repository split replace the earlier snapshot.
- 2026-11-01 (superseded): the app was `accs-system`, data lived only in memory with no database, and the app was split across seven module hostnames (`deltas/2026-11-01-subdomain-sites.md`, replaced by `deltas/2026-11-05-one-portal-host.md`).
