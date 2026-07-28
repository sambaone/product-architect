# Phased planning: atomization and task cards

ALWAYS read this when executing Step 4 and when opening every new phase. These rules exist
so the architecture lands in executable units without breaking the agent through context
overload: atoms (tasks) that compose molecules (phases) that compose the product.

## Hierarchy

| Unit | What it is | Closes with |
|---|---|---|
| **Task (atom)** | One agent session, one commit | DoD: tests green + build |
| **Phase (molecule)** | 5–8 tasks, one verifiable deliverable | E2E on staging → merge → tag |
| **Product** | Phases F0..Fn | `v1.0.0` = MVP in production |

## Atomic task rule

A task is atomic if it meets ALL of this. If it doesn't, it gets split BEFORE starting:

1. **Fits in one session** without compacting context — rule of thumb: ~3–5 files touched,
   a single commit.
2. **Touches a single area** of `AGENTS.md` (Front, Back, Data or Infra). If it crosses
   areas, it's two tasks with an explicit dependency (`Depends on:`).
3. **Can be described in 1–2 sentences** and its DoD is verifiable in isolation (its own
   unit tests).
4. **Leaves the project deployable.** Vertical slices: never "all the backend first and
   then the frontend".

## Task card (mandatory format in PROGRESS.md)

Every task of the current phase is a self-contained card: a fresh session must be able to
execute it cold by reading only `CLAUDE.md` + the card.

```markdown
### T3.2 — POST /api/quotes endpoint
Area: Back · Depends on: T3.1 · Status: ⬜
Objective: create quote scoped by user_id
Files: app/api/quotes/route.ts, lib/db/quotes.ts, tests/quotes.test.ts
DoD: creation unit test + authz 404 test green; build passes
Out of scope: form UI (T3.4), emails (F5)
```

- `Depends on:` orders execution and enables parallelizing independent work across agents.
- `Out of scope:` is the explicit brake against scope creep inside the session. Always say
  WHERE the excluded work lives (another task or another phase).
- Numbering `T<phase>.<n>` so tasks can be referenced without ambiguity.

## Phase card: E2E criteria up front

When opening a phase, BEFORE the first task, write in its PROGRESS.md block 2–4 **concrete,
verifiable E2E criteria** (what will be tested on the fixed staging URL to declare the
phase ready). The pre-merge verification executes exactly those criteria — nothing is left
to in-the-moment interpretation.

## Rolling planning

- Only the **current phase** gets broken down into detailed cards. Future phases live as
  one line each. Detailed plans made far in advance go stale.
- **When opening each phase:** propose the breakdown (cards + E2E criteria) and **wait for
  the user's OK** before starting the first task — same contract as the architecture
  approval in Step 1. It is the user's control point over the project, phase by phase.
- If a phase calls for more than ~8 tasks, the phase gets split in two.

## Hot split

If mid-task you discover it's bigger than estimated:

1. **Stop.** Don't keep inflating the session.
2. Split the task in PROGRESS.md (the current card shrinks; the rest becomes new card(s)
   with a dependency).
3. Commit what already meets DoD (tests green for what's committed).
4. Continue with the next sub-task — ideally in a fresh session.

Never re-discover scope silently: the split gets written down in PROGRESS.md.

## Per-session context hygiene

- **One session = one card** (or a few small cards from the same area).
- When closing a card: commit + update PROGRESS.md. Tasks from another area → new session.
- Project state lives in the files (`PROGRESS.md`, `memory/MEMORY.md`), never only in the
  conversation. Any session can die or get compacted without losing anything.
