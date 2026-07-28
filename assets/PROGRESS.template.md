# Progress — {{PROJECT_NAME}}

> Single source of truth for project state. Update when closing EVERY task.
> Statuses: ⬜ pending · 🔨 in progress · ✅ done (only with tests green)
> Only the current phase gets detailed into cards; future ones are one-liners (rolling
> planning — rules in `references/phase-planning.md` of the product-architect skill).

**Current phase:** F{{N}}
**Latest release:** — (none yet)

## F0 — Deployable skeleton
Branch: `feat/f0-skeleton` · Status: ⬜

- ⬜ Scaffold + governance (CLAUDE.md, ARCHITECTURE.md, PROGRESS.md, AGENTS.md, memory/)
- ⬜ GitHub repo + Vercel project (main→prod, staging→preview with fixed domain)
- ⬜ Integrations connected: {{INTEGRATIONS}}
- ⬜ Pipeline verified: Ready deploy on staging AND production

**Phase closing:** E2E on staging ✔ · user approval ✔ · merge `main` ✔ · tag `v0.0.1` ✔ · prod Ready ✔

## F1 — {{PHASE_1_NAME}}
Branch: `feat/f1-{{slug}}` · Status: ⬜

**Phase E2E criteria** (written when opening it; they are EXACTLY what gets verified on
staging before the merge):
- {{verifiable criterion 1, e.g. "new user signs up and sees their empty dashboard"}}
- {{verifiable criterion 2}}

### T1.1 — {{short title}}
Area: {{Front|Back|Data|Infra}} · Depends on: — · Status: ⬜
Objective: {{what ends up working, in 1–2 sentences}}
Files: {{~3–5 expected paths}}
DoD: {{concrete tests}} green · build passes
Out of scope: {{what is NOT done here and where it lives (Tx.y / Fx)}}

### T1.2 — {{short title}}
Area: {{...}} · Depends on: T1.1 · Status: ⬜
Objective: {{...}}
Files: {{...}}
DoD: {{...}}
Out of scope: {{...}}

**Phase closing:** E2E staging ✔ · approval ✔ · merge ✔ · tag `v0.X.0` ✔ · prod Ready ✔

<!-- When closing a phase, record here: date, tag, merge hash. -->

## Future phases

> One line per phase. They get broken down into cards ONLY when opened, with the user's OK.

- F2 — {{name}}: {{one line of scope}}
- F3 — {{name}}: {{one line of scope}}

## Release history

| Tag | Date | Phase | Merge |
|---|---|---|---|
