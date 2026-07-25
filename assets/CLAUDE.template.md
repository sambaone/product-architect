# {{NOMBRE_PROYECTO}}

{{DESCRIPCION_UNA_LINEA}}

## Contexto

- **Producto:** {{QUE_HACE_Y_PARA_QUIEN}}
- **Stack:** {{STACK}}
- **Fase actual:** ver `PROGRESS.md` (fuente de verdad del estado)
- **Arquitectura:** ver `ARCHITECTURE.md`
- **Memoria del proyecto:** `memory/MEMORY.md` — léela al iniciar sesión; regístrale decisiones y gotchas nuevos

## Entornos

| Entorno | Rama | URL |
|---|---|---|
| Producción | `main` | {{URL_PROD}} |
| Staging | `staging` | {{URL_STAGING}} |

Todo el trabajo de la fase actual se prueba en **staging**. A producción solo llega lo verificado.

## Reglas innegociables

1. **Una tarea NO está terminada hasta que sus tests unitarios corren y pasan.** Ejecuta los tests y muestra el output antes de decir "listo". Si la tarea no tiene tests, escribirlos es parte de la tarea.
2. **Nunca trabajes directo sobre `main` ni `staging`.** Ramas `feat/fN-descripcion` desde `staging`.
3. **No merge a `main` sin:** (a) verificación E2E en staging, y (b) aprobación explícita del usuario. En ese orden.
4. **Cada fase completada** → merge a `main` → tag `vX.Y.Z` → verificar deploy de producción en Ready → actualizar `PROGRESS.md` y `memory/MEMORY.md`.
5. **Integraciones siempre vía Vercel** (Marketplace / MCP / CLI). No conectar servicios por fuera si existe la integración.
6. **Commits convencionales:** `feat:`, `fix:`, `test:`, `chore:`, `docs:`.
7. **UI con shadcn/ui + Tailwind:** componentes vía `npx shadcn@latest add <componente>`; no reinventar botones/modales/formularios a mano.
8. En pruebas E2E multi-usuario: **confirmar la identidad de la sesión ANTES de cualquier operación destructiva** contra la base de datos.
9. **Una sesión = una tarjeta de `PROGRESS.md`** (o pocas tarjetas pequeñas de la misma área). Respeta el "Fuera de alcance" de la tarjeta. Si la tarea crece a mitad: parar, partirla en `PROGRESS.md`, commitear lo que ya cumple DoD y seguir con la sub-tarea (idealmente en sesión fresca). El estado del proyecto vive en los archivos, no en la conversación.
10. **Solo la fase actual se detalla en tarjetas.** Al abrir una fase nueva: proponer su descomposición (tarjetas atómicas + criterios E2E) y esperar el OK del usuario antes de la primera tarea.

## Definition of Done (por tarea)

- [ ] Código implementado
- [ ] Tests unitarios de la tarea escritos
- [ ] Tests ejecutados ahora y en verde (output mostrado)
- [ ] `npm run build` pasa
- [ ] `PROGRESS.md` actualizado

## Comandos

```bash
{{COMANDO_DEV}}        # desarrollo local
{{COMANDO_TEST}}       # tests unitarios
{{COMANDO_BUILD}}      # build de producción
```
