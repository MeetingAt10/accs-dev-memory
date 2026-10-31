# Current implementation state

Status: Observed; not a completed-feature list.
Last reviewed: 2026-11-01.

## Stack

`accs-system/frontend` is React 19, Vite 7, TypeScript, TanStack Router and Query, built on `@meetingat10/design-system`. `accs-system/backend` is Express 5 and TypeScript, run on AWS Lambda through `serverless-http`. Data is synthetic demo data; there is no application database, institutional identity integration, or AI provider.

## Deployment (dev)

CloudFormation stack `accs-dev` in `ap-southeast-1` (templates in `accs-architecture`). The API is an HTTP API plus one Lambda. Each site is a private S3 bucket behind CloudFront on its default `*.cloudfront.net` hostname; no domain, Route 53 zone, or certificate is configured. Pushes to `dev`/`main` on `accs-frontend` and `accs-backend` deploy through GitHub Actions using the `deploy` IAM user (`accs-architecture/iam/deploy-user-policy.json`).

## Module sites (2026-11-01)

The signed-in app is split into seven sites built from one SPA: Home (`portal` host label), Forums, Schedule, Cases, Evaluations, Records, Admin. All 58 DOC003 modules map to exactly one site; ADM-03 sits on Records (P35). Spec: `accs-docs/deltas/2026-11-01-subdomain-sites.md`.

- Site detection: build-time `VITE_SITE_ORIGINS` (stack output `SiteOrigins`), then the first host label, then unified mode on plain `localhost`.
- A route owned by another site does a full-page redirect to that site's origin. The session cookie lives on the API host (`SameSite=None` in production), so one sign-in covers every site.
- Backend `CORS_ORIGIN` is a comma-separated list; the stack passes all seven site URLs.
- The deploy policy lists distribution IDs explicitly. If a site stack is recreated, update `InvalidateCdn` and re-apply the policy.
- Checks: `frontend/src/lib/sites.test.ts` and `backend/tests/doc003-modules.test.ts`.

`accs-landing` is a separate Next.js site; its Figma notes are in `BRAND_REFERENCE.md`.

Sources: `accs-system/frontend/src/lib/sites.ts`, `accs-system/backend/src/env.ts`, `accs-architecture/cloudformation/envs/dev/main.yaml`. Recheck code before relying on this snapshot.
