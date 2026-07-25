# Flujo de Git: ramas, commits, tags y releases

Leer si hay dudas durante el ciclo de fase.

## Ramas

```
main      ← producción. Solo recibe merges desde staging, aprobados por el usuario.
staging   ← integración. Recibe PRs de las ramas de fase. Deploya a la URL fija de staging.
feat/fN-descripcion  ← trabajo diario. Una por fase (o por tarea grande dentro de la fase).
fix/descripcion      ← hotfixes. Salen de main, se mergean a main Y a staging.
```

Nunca commitear directo en `main` ni `staging`.

## Commits

Convención: `tipo(alcance opcional): descripción en minúsculas`

- `feat:` funcionalidad nueva · `fix:` corrección · `test:` solo tests
- `chore:` tooling/config · `docs:` documentación · `refactor:` sin cambio de comportamiento

Un commit por unidad coherente de trabajo (idealmente por tarea con su DoD cumplido), no
un mega-commit por fase. El mensaje dice QUÉ y, si no es obvio, POR QUÉ.

## Cierre de fase (checklist completo)

1. Todas las tareas de la fase en ✅ en `PROGRESS.md` (cada una pasó su DoD con tests).
2. PR `feat/fN-...` → `staging`. Revisar el diff completo antes de mergear.
3. Deploy de staging en Ready → verificación E2E en la URL fija de staging.
4. **Pedir aprobación explícita al usuario** mostrando: qué se verificó, resultado, y qué
   se va a mergear. No mergear a `main` sin su "sí".
5. PR `staging` → `main`, merge.
6. Tag: `git tag vX.Y.Z && git push --tags`
   - `v0.0.1` = F0 deployable · minor (`v0.X.0`) = fase completada · patch = hotfix
   - `v1.0.0` = MVP completo en producción con usuarios reales.
7. Verificar deploy de producción en Ready (no asumir: consultar Vercel).
8. Actualizar `PROGRESS.md` (historial de releases: tag, fecha, hash) y `memory/MEMORY.md`.

## Hotfix en producción

`fix/...` desde `main` → tests → merge a `main` → tag patch → verificar prod →
mergear también a `staging` para que no diverjan.

El DoD de tarea aplica igual a los hotfixes: test que reproduce el bug (falla antes,
pasa después) + suite en verde + build. Un hotfix sin test es deuda que vuelve.
