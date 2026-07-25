# Agentes — {{NOMBRE_PROYECTO}}

> Cómo repartir el trabajo entre agentes/sesiones de Claude Code sin que se pisen.
> La estructura del proyecto (ver `ARCHITECTURE.md`) está pensada para que estas áreas
> sean independientes.

## Áreas de trabajo

| Área | Alcance (carpetas) | Notas |
|---|---|---|
| Front | `app/`, `components/` | UI, páginas, estados de carga/error |
| Back | `app/api/`, `lib/server/` | Endpoints, lógica de negocio, authz |
| Datos | `lib/db/`, migraciones | Esquema, queries scopeadas por `user_id` |
| Infra | Vercel, integraciones, envs | Solo vía Vercel CLI/MCP |

## Reglas para todo agente

1. Lee `CLAUDE.md`, `PROGRESS.md` y `memory/MEMORY.md` antes de tocar código.
2. Trabaja SOLO en tu área y en la rama de fase asignada (`feat/fN-...`). Toma únicamente
   tarjetas de `PROGRESS.md` cuya `Área:` sea la tuya y cuyas dependencias (`Depende de:`)
   estén en ✅. Si necesitas tocar otra área, decláralo antes.
3. Aplica el Definition of Done de `CLAUDE.md` — nada de marcar tareas sin tests en verde.
4. Si descubres un gotcha o tomas una decisión de diseño, anótala en `memory/MEMORY.md`.
5. Si algo del plan no funciona, NO improvises un rediseño: repórtalo para regresar y
   ajustar `ARCHITECTURE.md` / `PROGRESS.md` primero.
