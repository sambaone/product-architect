# arquitecto-producto

> A [Claude Code](https://claude.com/claude-code) skill that turns a product idea into a fully scaffolded, governed project on Vercel — and then enforces engineering discipline for the entire life of the build: atomic tasks, tests before "done", staging before production, and explicit user approval before anything ships.

The skill content is written in **Spanish**, but Claude follows it regardless of the language you chat in. Feel free to fork and translate.

## The problem it solves

Agentic coding fails in predictable ways: projects without version control, tasks marked "done" that were never tested, features merged straight to production, and — the big one — **tasks so large that the agent's context degrades mid-execution and the build breaks**. This skill encodes the guardrails so every project starts (and stays) disciplined without you having to repeat yourself.

## What it does

When you start a new product, the skill:

1. **Interviews you briefly** if product context is missing (max 4–5 questions — it never invents scope).
2. **Proposes an architecture** in a fixed format (stack, data model, project structure, integrations, phases, explicit non-goals) and **waits for your approval**.
3. **Scaffolds the project**: Next.js + TypeScript + Tailwind + shadcn/ui by default, Git from minute zero, plus five governance files generated from templates:
   - `CLAUDE.md` — project rules every future session inherits
   - `ARCHITECTURE.md` — the approved proposal, verbatim
   - `PROGRESS.md` — phases and task cards with live status (single source of truth)
   - `AGENTS.md` — work areas so parallel agents don't step on each other
   - `memory/MEMORY.md` — decisions and gotchas that can't be deduced from code
4. **Wires GitHub + Vercel**: private repo, new Vercel project, `main` → production, `staging` → preview with a fixed domain, integrations via the Vercel Marketplace (Neon, Clerk, Upstash…), and verifies the full deploy pipeline **before writing any feature**.
5. **Plans the MVP in phases** (F0..Fn, where F0 is always a deployable end-to-end skeleton) using rolling planning and atomic task cards (see below).

Then, **during development**, it governs execution: how a task closes, how a phase closes, and when something reaches production.

## Core principles (non-negotiable)

1. **Git from minute zero.** Every project is a repo with a GitHub remote.
2. **A task is not done until its unit tests run and pass.** No tests? Writing them *is* part of the task.
3. **Staging and production, always.** Work is verified on a fixed staging URL; only verified work reaches production.
4. **No merge to `main` without E2E verification on staging + explicit user approval.** Verify first, ask second, merge last.
5. **Vercel-first.** One new Vercel project per product; DB/auth/cache via Vercel Marketplace integrations.
6. **Simple but scalable MVP.** Deployable from phase 0, structured to grow without rewrites (e.g. `user_id` scoping from the first migration).

## Atoms → molecules: how it keeps agents from breaking

The part that makes this skill different is `references/plan-fases.md`:

| Unit | What it is | Closes with |
|---|---|---|
| **Task (atom)** | One agent session, one commit, one work area, ~3–5 files | Definition of Done: tests green + build passes |
| **Phase (molecule)** | 5–8 tasks, one verifiable deliverable | E2E on staging → merge → version tag |
| **Product** | Phases F0..Fn | `v1.0.0` = MVP in production |

- **Atomic task rule** — if a task doesn't fit one session, one area, and its own isolated tests, it gets split *before* starting.
- **Self-contained task cards** in `PROGRESS.md` (`Objective / Files / DoD / Out of scope / Depends on`) — any fresh session can pick up a card cold, and "Out of scope" is an explicit brake on scope creep.
- **Rolling planning** — only the current phase is detailed into cards; future phases stay one-liners. Each phase's breakdown (plus its E2E acceptance criteria, written *up front*) is approved by the user before work starts.
- **Hot-split rule** — if a task grows mid-flight: stop, split it in `PROGRESS.md`, commit what already meets DoD, continue fresh.
- **Context hygiene** — one session ≈ one card; project state lives in files, never only in the conversation.

## Repository layout

```
SKILL.md                      # entry point: principles, workflow, execution rules
assets/
  CLAUDE.template.md          # project rules (inherited by every session)
  PROGRESS.template.md        # phases + task-card format
  AGENTS.template.md          # work areas for parallel agents
  MEMORY.template.md          # project memory (decisions, gotchas)
references/
  plan-fases.md               # atomic tasks, task cards, rolling planning
  vercel-setup.md             # environments, fixed staging domain, integration gotchas
  git-flow.md                 # branches, conventional commits, tags, releases, hotfixes
evals/
  evals.json                  # eval scenarios used to benchmark the skill
```

## Installation

```bash
git clone https://github.com/sambaone/arquitecto-producto.git ~/.claude/skills/arquitecto-producto
```

Claude Code picks it up automatically. Trigger it by asking things like *"let's start a new project"*, *"design the architecture for…"*, or *"set up the MVP"* — or invoke it directly with `/arquitecto-producto`.

## Requirements & assumptions

- [Claude Code](https://claude.com/claude-code) with the `gh` (GitHub CLI) and `vercel` CLIs authenticated.
- A Vercel account (the skill is deliberately Vercel-first).
- Opinionated defaults: Next.js App Router, TypeScript, Tailwind + shadcn/ui, Neon (Postgres), Clerk (auth), Upstash (Redis/rate limiting). Edit `SKILL.md` and the templates if your stack differs — the governance model works with any stack.

## Customizing

Everything opinionated lives in three places: the default stack (in `SKILL.md`, Step 1), the integration table (`references/vercel-setup.md`), and the templates (`assets/`). The discipline layer — atomic tasks, DoD, staging gates, approval before merge — is stack-agnostic.

## License

[MIT](LICENSE)
