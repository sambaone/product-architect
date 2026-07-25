# Setup de Vercel: entornos, integraciones y gotchas

Leer al ejecutar el Paso 3 del skill (GitHub + Vercel + integraciones).

## Proyecto nuevo

Cada producto = proyecto nuevo en Vercel (nunca reutilizar uno existente).

```bash
vercel link          # crear/vincular el proyecto (o usar el MCP de Vercel)
```

Si el MCP de Vercel está disponible en la sesión, preferirlo para crear el proyecto,
consultar deployments y leer logs — automatiza sin salir de Claude Code.

## Entornos: producción y staging

Vercel trae Production (rama `main`) y Preview (todas las demás). Para tener un staging
estable:

1. Crear la rama `staging` y pushearla.
2. Asignarle un dominio fijo al deployment de esa rama:
   ```bash
   vercel domains add staging-<proyecto>.vercel.app   # o vía dashboard: Settings → Domains → asignar a rama staging
   ```
   Así las pruebas E2E de cada fase tienen URL estable, no una URL de preview distinta
   por commit.
3. Variables de entorno: revisar qué valores difieren entre Production y Preview
   (ej. claves de test de Clerk en Preview, live en Production). `vercel env pull` para
   sincronizar local.

Verificación del pipeline (antes de escribir features): un commit trivial a `staging`
debe producir deploy Ready en la URL de staging; el merge a `main` debe producir deploy
Ready en producción. Confirmar ambos con `vercel ls` / MCP, no asumir.

## Integraciones (siempre vía Vercel)

| Necesidad | Servicio | Comando |
|---|---|---|
| Postgres | Neon | `vercel integration add neon` |
| Auth | Clerk | `vercel integration add clerk` |
| Rate limit / Redis | Upstash | `vercel integration add upstash` |
| Blob storage | Vercel Blob | nativo (`vercel blob`) |

Después de cada integración: `vercel env pull .env.local` y verificar que la app arranca.

## Gotchas conocidos (aprendidos en proyectos reales)

- **`vercel integration add` sobrescribe `.env.local` en cada ejecución.** Respaldar
  (`cp .env.local .env.local.bak`) antes de agregar una integración nueva y re-mergear
  a mano las variables que no vengan de Vercel.
- **Clerk + Next.js:** `createRouteMatcher` con patrones deprecados cambia entre
  versiones — revisar la doc de la versión instalada, no la memoria. Si se usa proxy
  (`proxy.ts`), configurarlo desde el inicio.
- **Clerk en Preview/staging:** usar las claves de test (instancia de desarrollo) en
  Preview y las live solo en Production, para poder probar cuentas sin ensuciar datos
  reales.
- **Rate limiting (Upstash):** las respuestas 429 devuelven body de texto plano — el
  cliente debe manejar respuestas no-JSON. En general, definir `onError` en los handlers
  para que los errores lleguen legibles al front.
- **BD compartida entre staging y prod:** si el MVP arranca con una sola base de datos,
  las pruebas E2E tocan datos reales. Confirmar SIEMPRE la identidad de la sesión antes
  de operaciones destructivas, y considerar rama de BD separada para staging (Neon
  branching) en cuanto haya usuarios reales.
- **Multi-tenancy:** incluir `user_id` (del proveedor de auth) NOT NULL + índice en las
  tablas desde la primera migración, con todas las queries scopeadas `fn(userId, …)` y
  authz que devuelve 404 (no 403) para recursos ajenos. Agregarlo después es una
  migración dolorosa.
