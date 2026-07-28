# Git flow: branches, commits, tags and releases

Read if in doubt during the phase cycle.

## Branches

```
main      ← production. Only receives merges from staging, approved by the user.
staging   ← integration. Receives PRs from phase branches. Deploys to the fixed staging URL.
feat/fN-description  ← daily work. One per phase (or per large task within the phase).
fix/description      ← hotfixes. Branch off main, merge to main AND to staging.
```

Never commit directly on `main` or `staging`.

## Commits

Convention: `type(optional scope): lowercase description`

- `feat:` new functionality · `fix:` correction · `test:` tests only
- `chore:` tooling/config · `docs:` documentation · `refactor:` no behavior change

One commit per coherent unit of work (ideally per task with its DoD met), not one
mega-commit per phase. The message says WHAT and, if not obvious, WHY.

## Phase closing (full checklist)

1. All tasks of the phase at ✅ in `PROGRESS.md` (each passed its DoD with tests).
2. PR `feat/fN-...` → `staging`. Review the full diff before merging.
3. Staging deploy Ready → E2E verification on the fixed staging URL.
4. **Ask the user for explicit approval** showing: what was verified, the result, and what
   is going to be merged. Do not merge to `main` without their "yes".
5. PR `staging` → `main`, merge.
6. Tag: `git tag vX.Y.Z && git push --tags`
   - `v0.0.1` = deployable F0 · minor (`v0.X.0`) = phase completed · patch = hotfix
   - `v1.0.0` = complete MVP in production with real users.
7. Verify the production deploy is Ready (don't assume: check Vercel).
8. Update `PROGRESS.md` (release history: tag, date, hash) and `memory/MEMORY.md`.

## Hotfix in production

`fix/...` from `main` → tests → merge to `main` → patch tag → verify prod →
also merge into `staging` so they don't diverge.

The task DoD applies equally to hotfixes: a test that reproduces the bug (fails before,
passes after) + suite green + build. A hotfix without a test is debt that comes back.
