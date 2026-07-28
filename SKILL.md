---
name: product-architect
description: >
  Designs the software architecture and scaffolds the complete foundation of a product to be
  built with Claude Code on Vercel. Use it WHENEVER the user wants to "create the
  architecture", "start a new project", "set up the project", "start the MVP", "design the
  product", "structure the development", or when there is already context about what a product
  will do and it's time to build it — even if they don't explicitly ask for "architecture".
  Generates CLAUDE.md, AGENTS.md, PROGRESS.md, ARCHITECTURE.md and project memory; enforces
  Git + GitHub with per-phase branches, staging and production environments on Vercel,
  integrations via Vercel (Neon, Clerk, Upstash), and the rule that no task is done until its
  unit tests pass. It also applies DURING development: when a task or phase is finished, this
  skill defines how it gets verified, merged, tagged and deployed.
---

# Product Architect

Turns product context into a project ready to be developed with Claude Code: proposed
architecture, Git repo, memory/instruction files, phased plan, and a staging → production
pipeline on Vercel. Afterwards, it governs execution: how a task closes, how a phase closes,
and when something reaches production.

## Non-negotiable principles

These rules are not negotiated or relaxed even if the user is in a hurry. The user lets
Claude propose the architecture, but these rules are theirs and they are firm:

1. **Git from minute zero.** Every project is a Git repo with a GitHub remote. No loose
   folders without version control.
2. **A task is NOT done until its unit tests run and pass.** Never say "done" or mark a task
   as complete without having executed the tests and shown the result. If the task has no
   tests, writing them IS part of the task.
3. **Staging and production, always.** Everything under development (tasks of the current
   phase) is verified on staging. Only verified work reaches production.
4. **No merge to `main` without E2E verification on staging + explicit user approval.**
   Verify first, ask second, merge last. (Direct user feedback — firm.)
5. **Vercel-first.** A new Vercel project for every product. Database, auth, cache, etc. are
   connected via Vercel integrations (Neon, Clerk, Upstash…) and managed with the Vercel MCP
   / `vercel` CLI to automate as much as possible.
6. **Simple but scalable MVP.** The architecture must deploy to Vercel quickly from phase 0,
   but with a data model, frontend and backend designed to grow without rewrites.

## Workflow

### Step 0 — Confirm the context

Before proposing anything, verify you know: what the product does, who it's for, and which
features make up the MVP. If something is missing, run a short interview (max 4–5 concrete
questions). Do not invent scope.

### Step 1 — Propose the architecture (and wait for the OK)

Present a proposal with exactly this structure and wait for user approval before creating
any files:

```
## Architecture proposal: [name]
### Stack
(default: Next.js App Router + TypeScript, with UI in Tailwind CSS + shadcn/ui;
justify if you propose something else)
### Initial data model
(tables/entities with key fields; include user_id from day 1 if there is auth —
multi-tenancy is cheap now, very expensive later)
### Project structure
(front/back/lib/tests folders — designed so multiple agents can work in
parallel without stepping on each other)
### Integrations (via Vercel)
(what gets connected and with what: DB → Neon, Auth → Clerk, Rate limit/cache → Upstash…)
### Phases
(F0..Fn, one line per phase; F0 ALWAYS = deployable end-to-end skeleton)
### Out of MVP scope
(explicit, to control scope)
```

Design rules: prefer boring and proven; every piece must be able to scale (serverless
Postgres, managed auth, serverless functions) without painful migration; avoid
microservices, queues and speculative abstractions in an MVP.

Frontend: UI components are built with **shadcn/ui** on Tailwind (installed with
`npx shadcn@latest add <component>`, never hand-copied or reinvented). This gives visual
consistency across projects and accessible components from day 1. Record the chosen theme
(colors/typography) in `ARCHITECTURE.md`. If the session has the `vercel:shadcn` skill
available, use it when building the UI.

### Step 2 — Project scaffold

With the architecture approved:

1. Create the project (`create-next-app` or equivalent) and **immediately** `git init` if
   the tool didn't do it.
2. Create the governance files from the templates in `assets/` (fill the `{{...}}`
   placeholders with the project's data):
   - `CLAUDE.md` ← `assets/CLAUDE.template.md` — project instructions (includes these
     non-negotiable rules so EVERY future session inherits them)
   - `ARCHITECTURE.md` — the approved proposal from Step 1, verbatim
   - `PROGRESS.md` ← `assets/PROGRESS.template.md` — phases and tasks with status
   - `AGENTS.md` ← `assets/AGENTS.template.md` — which agents/roles work which areas
   - `memory/MEMORY.md` ← `assets/MEMORY.template.md` — project decisions and gotchas
3. Branches: `main` (production) and `staging` (integration). Daily work happens on
   `feat/fN-description` branches.
4. First commit on `main`: `chore: initial scaffold + project governance`.

### Step 3 — GitHub + Vercel + integrations

1. `gh repo create <name> --private --source=. --push` (confirm the name with the user if
   it's not obvious).
2. **New** Vercel project linked to the repo (`vercel link` / Vercel MCP).
   - `main` → Production.
   - `staging` → Preview with a fixed domain (assign the domain
     `staging-<project>.vercel.app` to the branch so phase testing has a stable URL).
3. Connect integrations according to the architecture (`vercel integration add neon`,
   Clerk, Upstash…) and `vercel env pull`. **Known gotcha:** every `integration add` may
   overwrite `.env.local` — back it up first. More detail in `references/vercel-setup.md`.
4. Verify the full pipeline BEFORE writing features: push to `staging` → Preview deploy
   Ready → merge to `main` → Production deploy Ready. If the skeleton doesn't deploy,
   nothing else matters yet.

### Step 4 — Phased plan in PROGRESS.md (rolling planning)

Read `references/phase-planning.md` BEFORE this step — it defines the atomic task rule,
the task-card format and rolling planning. Summary of the contract:

1. Break the MVP into phases F0..Fn (F0 = deployable skeleton, already done in Step 3),
   each phase = one verifiable deliverable, **5–8 tasks maximum**.
2. Only the **current phase** gets detailed into self-contained task cards (atom = one
   session, one commit, one area, ~3–5 files, DoD with its own tests). Future phases stay
   as one-liners.
3. Every phase opens with its **E2E criteria** written up front — that is exactly what will
   be verified on staging before the merge.
4. Each phase's breakdown is presented to the user and you **wait for their OK** before
   starting the first task (same contract as Step 1 with the architecture).

Write the plan in `PROGRESS.md` (template format) and commit.

## Execution rules (life of the project)

These rules apply in ALL development sessions of the project (they are copied into the
project's CLAUDE.md so they get inherited):

**Definition of Done for a task:**
- Code implemented + unit tests for that task written.
- Tests executed right now (not "they should pass") and green — show the output.
- Local build passes (`npm run build` or equivalent).
- `PROGRESS.md` updated. Only then can you say "done".

**Phase cycle:**
0. When opening the phase: break it down into atomic task cards + E2E criteria (format and
   rules in `references/phase-planning.md`) and **wait for the user's OK**.
1. Branch `feat/fN-description` from `staging`.
2. Tasks one by one — ideally **one card per session** —, each with its Definition of Done
   and its commit (conventional messages: `feat:`, `fix:`, `test:`, `chore:`). If a task
   grows mid-flight: stop, split it in `PROGRESS.md`, commit what already meets DoD, and
   continue with the sub-task (hot split).
3. Phase complete → PR to `staging` → verify on the fixed staging URL **exactly the E2E
   criteria written when the phase was opened**.
   In multi-user testing: confirm the session's identity BEFORE any destructive operation
   against the database.
4. Verified on staging → **ask the user for explicit approval** to merge to `main`.
5. Approved → merge to `main` → tag `vX.Y.Z` → verify the production deploy is Ready →
   update `PROGRESS.md` and `memory/MEMORY.md` (decisions, discovered gotchas).

**Never:**
- Mark tasks as done without running tests.
- Start a task that doesn't meet the atomic task rule — split it first
  (`references/phase-planning.md`).
- Merge to `main` without going through staging.
- Work directly on `main` or `staging` (always phase branches).
- Connect services outside Vercel if the integration exists in its Marketplace.

## References

- `references/phase-planning.md` — atomic task rule, card format, per-phase E2E criteria,
  rolling planning, hot split and context hygiene. Read it when executing Step 4 and when
  opening every phase.
- `references/vercel-setup.md` — detail on environments, fixed staging domain, integrations
  (Neon/Clerk/Upstash) and known gotchas. Read it when executing Step 3.
- `references/git-flow.md` — branch flow, commit convention, tags and releases. Read it
  if in doubt during the phase cycle.
- `assets/*.template.md` — templates for the governance files. Always use them in Step 2
  instead of inventing the format.
