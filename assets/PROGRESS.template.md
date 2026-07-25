# Progreso — {{NOMBRE_PROYECTO}}

> Fuente de verdad del estado del proyecto. Actualizar al cerrar CADA tarea.
> Estados: ⬜ pendiente · 🔨 en curso · ✅ terminada (solo con tests en verde)
> Solo la fase actual se detalla en tarjetas; las futuras son una línea (planificación
> rodante — reglas en `references/plan-fases.md` del skill arquitecto-producto).

**Fase actual:** F{{N}}
**Último release:** — (ninguno aún)

## F0 — Esqueleto deployable
Rama: `feat/f0-esqueleto` · Estado: ⬜

- ⬜ Scaffold + gobernanza (CLAUDE.md, ARCHITECTURE.md, PROGRESS.md, AGENTS.md, memory/)
- ⬜ Repo GitHub + proyecto Vercel (main→prod, staging→preview con dominio fijo)
- ⬜ Integraciones conectadas: {{INTEGRACIONES}}
- ⬜ Pipeline verificado: deploy Ready en staging Y producción

**Cierre de fase:** E2E en staging ✔ · aprobación usuario ✔ · merge `main` ✔ · tag `v0.0.1` ✔ · prod Ready ✔

## F1 — {{NOMBRE_FASE_1}}
Rama: `feat/f1-{{slug}}` · Estado: ⬜

**Criterios E2E de la fase** (escritos al abrirla; son EXACTAMENTE lo que se verifica en
staging antes del merge):
- {{criterio verificable 1, ej: "usuario nuevo se registra y ve su dashboard vacío"}}
- {{criterio verificable 2}}

### T1.1 — {{título corto}}
Área: {{Front|Back|Datos|Infra}} · Depende de: — · Estado: ⬜
Objetivo: {{qué queda funcionando, en 1–2 frases}}
Archivos: {{~3–5 rutas previstas}}
DoD: {{tests concretos}} en verde · build pasa
Fuera de alcance: {{qué NO se hace aquí y dónde queda (Tx.y / Fx)}}

### T1.2 — {{título corto}}
Área: {{...}} · Depende de: T1.1 · Estado: ⬜
Objetivo: {{...}}
Archivos: {{...}}
DoD: {{...}}
Fuera de alcance: {{...}}

**Cierre de fase:** E2E staging ✔ · aprobación ✔ · merge ✔ · tag `v0.X.0` ✔ · prod Ready ✔

<!-- Al cerrar una fase, anotar aquí: fecha, tag, hash del merge. -->

## Fases futuras

> Una línea por fase. Se descomponen en tarjetas SOLO al abrirlas, con OK del usuario.

- F2 — {{nombre}}: {{una línea de alcance}}
- F3 — {{nombre}}: {{una línea de alcance}}

## Historial de releases

| Tag | Fecha | Fase | Merge |
|---|---|---|---|
