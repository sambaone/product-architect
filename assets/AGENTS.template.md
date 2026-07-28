# Agents — {{PROJECT_NAME}}

> How to split the work across Claude Code agents/sessions without them stepping on each
> other. The project structure (see `ARCHITECTURE.md`) is designed so these areas are
> independent.

## Work areas

| Area | Scope (folders) | Notes |
|---|---|---|
| Front | `app/`, `components/` | UI, pages, loading/error states |
| Back | `app/api/`, `lib/server/` | Endpoints, business logic, authz |
| Data | `lib/db/`, migrations | Schema, queries scoped by `user_id` |
| Infra | Vercel, integrations, envs | Only via Vercel CLI/MCP |

## Rules for every agent

1. Read `CLAUDE.md`, `PROGRESS.md` and `memory/MEMORY.md` before touching code.
2. Work ONLY in your area and on the assigned phase branch (`feat/fN-...`). Take only
   cards from `PROGRESS.md` whose `Area:` is yours and whose dependencies (`Depends on:`)
   are at ✅. If you need to touch another area, declare it first.
3. Apply the Definition of Done from `CLAUDE.md` — no marking tasks without tests green.
4. If you discover a gotcha or make a design decision, record it in `memory/MEMORY.md`.
5. If something in the plan doesn't work, do NOT improvise a redesign: report it so we can
   go back and adjust `ARCHITECTURE.md` / `PROGRESS.md` first.
