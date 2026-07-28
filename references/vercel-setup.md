# Vercel setup: environments, integrations and gotchas

Read when executing Step 3 of the skill (GitHub + Vercel + integrations).

## New project

Every product = a new Vercel project (never reuse an existing one).

```bash
vercel link          # create/link the project (or use the Vercel MCP)
```

If the Vercel MCP is available in the session, prefer it to create the project, check
deployments and read logs — it automates without leaving Claude Code.

## Environments: production and staging

Vercel ships with Production (branch `main`) and Preview (all other branches). To get a
stable staging:

1. Create the `staging` branch and push it.
2. Assign a fixed domain to that branch's deployment:
   ```bash
   vercel domains add staging-<project>.vercel.app   # or via dashboard: Settings → Domains → assign to the staging branch
   ```
   This way each phase's E2E testing has a stable URL, not a different preview URL per
   commit.
3. Environment variables: review which values differ between Production and Preview
   (e.g. Clerk test keys in Preview, live keys in Production). `vercel env pull` to sync
   locally.

Pipeline verification (before writing features): a trivial commit to `staging` must produce
a Ready deploy on the staging URL; the merge to `main` must produce a Ready deploy in
production. Confirm both with `vercel ls` / MCP — don't assume.

## Integrations (always via Vercel)

| Need | Service | Command |
|---|---|---|
| Postgres | Neon | `vercel integration add neon` |
| Auth | Clerk | `vercel integration add clerk` |
| Rate limit / Redis | Upstash | `vercel integration add upstash` |
| Blob storage | Vercel Blob | native (`vercel blob`) |

After each integration: `vercel env pull .env.local` and verify the app boots.

## Known gotchas (learned in real projects)

- **`vercel integration add` overwrites `.env.local` on every run.** Back it up
  (`cp .env.local .env.local.bak`) before adding a new integration and hand-merge back the
  variables that don't come from Vercel.
- **Clerk + Next.js:** `createRouteMatcher` with deprecated patterns changes between
  versions — check the docs of the installed version, not memory. If using a proxy
  (`proxy.ts`), configure it from the start.
- **Clerk in Preview/staging:** use the test keys (development instance) in Preview and
  the live keys only in Production, so you can test accounts without polluting real data.
- **Rate limiting (Upstash):** 429 responses return a plain-text body — the client must
  handle non-JSON responses. In general, define `onError` in handlers so errors reach the
  frontend in readable form.
- **Database shared between staging and prod:** if the MVP starts with a single database,
  E2E tests touch real data. ALWAYS confirm the session's identity before destructive
  operations, and consider a separate DB branch for staging (Neon branching) as soon as
  there are real users.
- **Multi-tenancy:** include `user_id` (from the auth provider) NOT NULL + index on the
  tables from the first migration, with all queries scoped `fn(userId, …)` and authz that
  returns 404 (not 403) for foreign resources. Adding it later is a painful migration.
