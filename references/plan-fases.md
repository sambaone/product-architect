# Plan por fases: atomización y tarjetas de tarea

Leer SIEMPRE al ejecutar el Paso 4 y al abrir cada fase nueva. Estas reglas existen para
que la arquitectura aterrice en unidades ejecutables sin romper al agente por contexto:
átomos (tareas) que componen moléculas (fases) que componen el producto.

## Jerarquía

| Unidad | Qué es | Cierra con |
|---|---|---|
| **Tarea (átomo)** | Una sesión de agente, un commit | DoD: tests en verde + build |
| **Fase (molécula)** | 5–8 tareas, un entregable verificable | E2E staging → merge → tag |
| **Producto** | Fases F0..Fn | `v1.0.0` = MVP en producción |

## Regla de tarea atómica

Una tarea es atómica si cumple TODO esto. Si no cumple, se parte ANTES de empezar:

1. **Cabe en una sesión** sin compactar contexto — regla práctica: ~3–5 archivos tocados,
   un solo commit.
2. **Toca una sola área** de `AGENTS.md` (Front, Back, Datos o Infra). Si cruza áreas,
   son dos tareas con dependencia explícita (`Depende de:`).
3. **Se describe en 1–2 frases** y su DoD es verificable de forma aislada (sus propios
   tests unitarios).
4. **Deja el proyecto deployable.** Slices verticales: nunca "todo el backend primero
   y luego el front".

## Tarjeta de tarea (formato obligatorio en PROGRESS.md)

Cada tarea de la fase actual es una tarjeta autocontenida: una sesión nueva debe poder
ejecutarla en frío leyendo solo `CLAUDE.md` + la tarjeta.

```markdown
### T3.2 — Endpoint POST /api/quotes
Área: Back · Depende de: T3.1 · Estado: ⬜
Objetivo: crear cotización scopeada por user_id
Archivos: app/api/quotes/route.ts, lib/db/quotes.ts, tests/quotes.test.ts
DoD: test unitario de creación + authz 404 en verde; build pasa
Fuera de alcance: UI del formulario (T3.4), emails (F5)
```

- `Depende de:` ordena la ejecución y habilita paralelizar lo independiente entre agentes.
- `Fuera de alcance:` es el freno explícito contra el scope creep dentro de la sesión.
  Siempre di DÓNDE queda lo excluido (otra tarea u otra fase).
- Numeración `T<fase>.<n>` para poder referirse a tareas sin ambigüedad.

## Tarjeta de fase: criterios E2E por adelantado

Al abrir una fase, ANTES de la primera tarea, escribir en su bloque de PROGRESS.md 2–4
**criterios E2E concretos y verificables** (qué se probará en la URL fija de staging para
declarar la fase lista). La verificación pre-merge ejecuta exactamente esos criterios —
no queda a interpretación del momento.

## Planificación rodante

- Solo la **fase actual** se descompone en tarjetas detalladas. Las fases futuras viven
  como una línea cada una. Los planes detallados por adelantado caducan.
- **Al abrir cada fase:** proponer la descomposición (tarjetas + criterios E2E) y
  **esperar el OK del usuario** antes de empezar la primera tarea — mismo contrato que
  la aprobación de arquitectura del Paso 1. Es el punto de control del usuario sobre
  el proyecto, fase por fase.
- Si una fase pide más de ~8 tareas, la fase se parte en dos.

## División en caliente

Si a mitad de una tarea se descubre que es más grande de lo estimado:

1. **Parar.** No seguir inflando la sesión.
2. Partir la tarea en PROGRESS.md (la tarjeta actual se reduce; lo demás se vuelve
   tarjeta(s) nueva(s) con dependencia).
3. Commitear lo que ya cumple DoD (tests en verde de lo commiteado).
4. Continuar con la sub-tarea siguiente — idealmente en sesión fresca.

Nunca redescubrir el alcance en silencio: la partición queda escrita en PROGRESS.md.

## Higiene de contexto por sesión

- **Una sesión = una tarjeta** (o pocas tarjetas pequeñas de la misma área).
- Al cerrar tarjeta: commit + actualizar PROGRESS.md. Tareas de otra área → sesión nueva.
- El estado del proyecto vive en los archivos (`PROGRESS.md`, `memory/MEMORY.md`), nunca
  solo en la conversación. Cualquier sesión puede morir o compactarse sin perder nada.
