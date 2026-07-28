# {{PROJECT_NAME}}

{{ONE_LINE_DESCRIPTION}}

## Context

- **Product:** {{WHAT_IT_DOES_AND_FOR_WHOM}}
- **Stack:** {{STACK}}
- **Current phase:** see `PROGRESS.md` (single source of truth for project state)
- **Architecture:** see `ARCHITECTURE.md`
- **Project memory:** `memory/MEMORY.md` — read it at session start; record new decisions and gotchas in it

## Environments

| Environment | Branch | URL |
|---|---|---|
| Production | `main` | {{PROD_URL}} |
| Staging | `staging` | {{STAGING_URL}} |

All work of the current phase is verified on **staging**. Only verified work reaches production.

## Non-negotiable rules

1. **A task is NOT done until its unit tests run and pass.** Execute the tests and show the output before saying "done". If the task has no tests, writing them is part of the task.
2. **Never work directly on `main` or `staging`.** Branches `feat/fN-description` from `staging`.
3. **No merge to `main` without:** (a) E2E verification on staging, and (b) explicit user approval. In that order.
4. **Every completed phase** → merge to `main` → tag `vX.Y.Z` → verify the production deploy is Ready → update `PROGRESS.md` and `memory/MEMORY.md`.
5. **Integrations always via Vercel** (Marketplace / MCP / CLI). Don't connect services outside it if the integration exists.
6. **Conventional commits:** `feat:`, `fix:`, `test:`, `chore:`, `docs:`.
7. **UI with shadcn/ui + Tailwind:** components via `npx shadcn@latest add <component>`; don't reinvent buttons/modals/forms by hand.
8. In multi-user E2E testing: **confirm the session's identity BEFORE any destructive operation** against the database.
9. **One session = one card from `PROGRESS.md`** (or a few small cards from the same area). Respect the card's "Out of scope". If the task grows mid-flight: stop, split it in `PROGRESS.md`, commit what already meets DoD and continue with the sub-task (ideally in a fresh session). Project state lives in the files, not in the conversation.
10. **Only the current phase gets detailed into cards.** When opening a new phase: propose its breakdown (atomic cards + E2E criteria) and wait for the user's OK before the first task.

## Definition of Done (per task)

- [ ] Code implemented
- [ ] Unit tests for the task written
- [ ] Tests executed now and green (output shown)
- [ ] `npm run build` passes
- [ ] `PROGRESS.md` updated

## Commands

```bash
{{DEV_COMMAND}}        # local development
{{TEST_COMMAND}}       # unit tests
{{BUILD_COMMAND}}      # production build
```
