---
name: arquitecto-producto
description: >
  Diseña la arquitectura de software y deja listo el andamiaje completo de un producto para
  desarrollarlo con Claude Code sobre Vercel. Úsalo SIEMPRE que el usuario quiera "crear la
  arquitectura", "iniciar un proyecto nuevo", "montar el proyecto", "empezar el MVP", "diseñar
  el producto", "estructurar el desarrollo", o cuando ya exista contexto de qué hará un producto
  y toque pasar a construirlo — aunque no pida explícitamente "arquitectura". Genera CLAUDE.md,
  AGENTS.md, PROGRESS.md, ARCHITECTURE.md y memoria de proyecto; impone Git + GitHub con ramas
  por fase, ambientes staging y producción en Vercel, integraciones vía Vercel (Neon, Clerk,
  Upstash) y la regla de que ninguna tarea está terminada sin tests unitarios pasando. También
  aplica DURANTE el desarrollo: cuando se termina una tarea o fase, este skill define cómo se
  verifica, se mergea, se taggea y se deploya.
---

# Arquitecto de Producto

Convierte el contexto de un producto en un proyecto listo para desarrollarse con Claude Code:
arquitectura propuesta, repo con Git, archivos de memoria/instrucciones, plan por fases, y
pipeline staging → producción en Vercel. Después, gobierna la ejecución: cómo se cierra una
tarea, cómo se cierra una fase, y cuándo algo llega a producción.

## Principios innegociables

Estas reglas no se negocian ni se relajan aunque el usuario tenga prisa. El usuario deja que
Claude proponga la arquitectura, pero estas reglas son suyas y son firmes:

1. **Git desde el minuto cero.** Todo proyecto es un repo Git con GitHub remoto. Nada de
   carpetas sueltas sin control de cambios.
2. **Una tarea NO está terminada hasta que sus tests unitarios corren y pasan.** Nunca digas
   "listo" o marques una tarea como completa sin haber ejecutado los tests y mostrado el
   resultado. Si no hay tests para esa tarea, escribirlos ES parte de la tarea.
3. **Staging y producción siempre.** Todo lo que está en desarrollo (tareas de la fase actual)
   se prueba en staging. A producción solo llega lo verificado.
4. **No merge a `main` sin verificación E2E en staging + aprobación explícita del usuario.**
   Verificar primero, preguntar después, mergear al final. (Ver [[no-merge-prod-sin-verificacion]]
   en la memoria del usuario — es feedback directo de él.)
5. **Vercel-first.** Proyecto nuevo en Vercel por cada producto. Base de datos, auth, cache,
   etc. se conectan vía integraciones de Vercel (Neon, Clerk, Upstash…) y se gestionan con el
   MCP de Vercel / CLI `vercel` para automatizar al máximo.
6. **MVP simple pero escalable.** La arquitectura debe deployarse en Vercel rápidamente desde
   la fase 0, pero con estructura de datos, front y back pensada para crecer sin reescribir.

## Flujo de trabajo

### Paso 0 — Confirmar el contexto

Antes de proponer nada, verifica que tienes: qué hace el producto, para quién es, y qué
features componen el MVP. Si algo falta, haz una entrevista breve (máximo 4-5 preguntas
concretas). No inventes alcance.

### Paso 1 — Proponer la arquitectura (y esperar el OK)

Presenta una propuesta con exactamente esta estructura y espera aprobación del usuario antes
de crear archivos:

```
## Propuesta de arquitectura: [nombre]
### Stack
(por defecto: Next.js App Router + TypeScript, con UI en Tailwind CSS + shadcn/ui;
justifica si propones otra cosa)
### Modelo de datos inicial
(tablas/entidades con campos clave; incluir user_id desde el día 1 si hay auth —
multi-tenancy barato ahora, carísimo después)
### Estructura del proyecto
(carpetas front/back/lib/tests — pensada para que varios agentes trabajen en
paralelo sin pisarse)
### Integraciones (vía Vercel)
(qué se conecta y con qué: DB → Neon, Auth → Clerk, Rate limit/cache → Upstash…)
### Fases
(F0..Fn, una línea por fase; F0 SIEMPRE = esqueleto deployable end-to-end)
### Qué queda fuera del MVP
(explícito, para controlar alcance)
```

Reglas de diseño: prefiere lo aburrido y probado; cada pieza debe poder escalar (Postgres
serverless, auth gestionada, funciones serverless) sin migración dolorosa; evita
microservicios, colas y abstracciones especulativas en un MVP.

Front: los componentes de UI se construyen con **shadcn/ui** sobre Tailwind (instalados
con `npx shadcn@latest add <componente>`, no copiados a mano ni reinventados). Esto da
consistencia visual entre proyectos y componentes accesibles desde el día 1. Registrar
en `ARCHITECTURE.md` el tema (colores/tipografía) elegido. Si la sesión tiene el skill
`vercel:shadcn` disponible, úsalo al montar la UI.

### Paso 2 — Scaffold del proyecto

Con la arquitectura aprobada:

1. Crear el proyecto (`create-next-app` o equivalente) e **inmediatamente** `git init` si la
   herramienta no lo hizo.
2. Crear los archivos de gobernanza desde las plantillas en `assets/` (rellena los
   placeholders `{{...}}` con los datos del proyecto):
   - `CLAUDE.md` ← `assets/CLAUDE.template.md` — instrucciones del proyecto (incluye estas
     reglas innegociables para que TODA sesión futura las herede)
   - `ARCHITECTURE.md` — la propuesta aprobada del Paso 1, tal cual
   - `PROGRESS.md` ← `assets/PROGRESS.template.md` — fases y tareas con estado
   - `AGENTS.md` ← `assets/AGENTS.template.md` — qué agentes/roles trabajan qué áreas
   - `memory/MEMORY.md` ← `assets/MEMORY.template.md` — decisiones y gotchas del proyecto
3. Ramas: `main` (producción) y `staging` (integración). El trabajo diario va en ramas
   `feat/fN-descripcion`.
4. Primer commit en `main`: `chore: scaffold inicial + gobernanza del proyecto`.

### Paso 3 — GitHub + Vercel + integraciones

1. `gh repo create <nombre> --private --source=. --push` (confirma el nombre con el usuario
   si no es obvio).
2. Proyecto **nuevo** en Vercel vinculado al repo (`vercel link` / MCP de Vercel).
   - `main` → Production.
   - `staging` → Preview con dominio fijo (asignar dominio `staging-<proyecto>.vercel.app`
     a la rama para que las pruebas de fase tengan URL estable).
3. Conectar integraciones según la arquitectura (`vercel integration add neon`, Clerk,
   Upstash…) y `vercel env pull`. **Gotcha conocido:** cada `integration add` puede
   sobrescribir `.env.local` — respáldalo antes. Más detalle en `references/vercel-setup.md`.
4. Verificar el pipeline completo ANTES de escribir features: push a `staging` → deploy
   Preview Ready → merge a `main` → deploy Production Ready. Si el esqueleto no deploya,
   nada más importa todavía.

### Paso 4 — Plan por fases en PROGRESS.md (planificación rodante)

Lee `references/plan-fases.md` ANTES de este paso — define la regla de tarea atómica,
el formato de tarjeta y la planificación rodante. Resumen del contrato:

1. Descompón el MVP en fases F0..Fn (F0 = esqueleto deployable, ya hecho en el Paso 3),
   cada fase = un entregable verificable, **5–8 tareas máximo**.
2. Solo la **fase actual** se detalla en tarjetas de tarea autocontenidas (átomo = una
   sesión, un commit, una área, ~3–5 archivos, DoD con tests propios). Las fases futuras
   quedan como una línea.
3. Cada fase abre con sus **criterios E2E** escritos por adelantado — eso es lo que se
   verificará en staging antes del merge.
4. La descomposición de cada fase se presenta al usuario y se **espera su OK** antes de
   empezar la primera tarea (mismo contrato que el Paso 1 con la arquitectura).

Escribe el plan en `PROGRESS.md` (formato de la plantilla) y haz commit.

## Reglas de ejecución (vida del proyecto)

Estas reglas aplican en TODAS las sesiones de desarrollo del proyecto (van copiadas en el
CLAUDE.md del proyecto para que se hereden):

**Definition of Done de una tarea:**
- Código implementado + tests unitarios de esa tarea escritos.
- Tests ejecutados en este momento (no "deberían pasar") y en verde — muestra el output.
- Build local pasa (`npm run build` o equivalente).
- `PROGRESS.md` actualizado. Solo entonces se puede decir "terminada".

**Ciclo de una fase:**
0. Al abrir la fase: descomponerla en tarjetas de tarea atómicas + criterios E2E
   (formato y reglas en `references/plan-fases.md`) y **esperar el OK del usuario**.
1. Rama `feat/fN-descripcion` desde `staging`.
2. Tareas una a una — idealmente **una tarjeta por sesión** —, cada una con su
   Definition of Done y su commit (mensajes convencionales: `feat:`, `fix:`, `test:`,
   `chore:`). Si una tarea crece a mitad: parar, partirla en `PROGRESS.md`, commitear
   lo que ya cumple DoD, y seguir con la sub-tarea (división en caliente).
3. Fase completa → PR a `staging` → verificar en la URL fija de staging **exactamente
   los criterios E2E escritos al abrir la fase**.
   En pruebas multi-usuario: confirmar la identidad de la sesión ANTES de cualquier
   operación destructiva contra la base de datos.
4. Verificado en staging → **pedir aprobación explícita al usuario** para mergear a `main`.
5. Aprobado → merge a `main` → tag `vX.Y.Z` → verificar el deploy de producción en Ready →
   actualizar `PROGRESS.md` y `memory/MEMORY.md` (decisiones, gotchas descubiertos).

**Nunca:**
- Marcar tareas como terminadas sin correr tests.
- Empezar una tarea que no cumpla la regla de tarea atómica — partirla primero
  (`references/plan-fases.md`).
- Mergear a `main` sin pasar por staging.
- Trabajar directo sobre `main` o `staging` (siempre ramas de fase).
- Conectar servicios por fuera de Vercel si existe la integración en su Marketplace.

## Referencias

- `references/plan-fases.md` — regla de tarea atómica, formato de tarjeta, criterios E2E
  por fase, planificación rodante, división en caliente e higiene de contexto. Léelo al
  ejecutar el Paso 4 y al abrir cada fase.
- `references/vercel-setup.md` — detalle de entornos, dominio fijo de staging, integraciones
  (Neon/Clerk/Upstash) y gotchas conocidos. Léelo al ejecutar el Paso 3.
- `references/git-flow.md` — flujo de ramas, convención de commits, tags y releases. Léelo
  si hay dudas durante el ciclo de fase.
- `assets/*.template.md` — plantillas de los archivos de gobernanza. Úsalas siempre en el
  Paso 2 en lugar de inventar el formato.
