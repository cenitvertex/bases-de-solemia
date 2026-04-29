# AGENTS.md — Solemia (boilerplate base)

> **Doble propósito:** (1) En el repo `bases-de-solemia`: es el contexto para asistentes IA que mantienen los docs internos. (2) Para cada repo cliente `cliente-[slug]`: este archivo se copia a la raíz del repo cliente y se complementa con una sección "Específico del proyecto" arriba.

## Stack

- **L1 Automatizaciones:** n8n self-hosted (Hetzner CCX13, queue mode + Postgres + Redis). Make/Zapier en casos específicos.
- **L2 Empleados IA:** brain en Next.js (Vercel AI SDK 6) + adapters por canal. Modelos vía Helicone Gateway (Haiku 4.5 default → Sonnet 4.6 fallback, routing 70/20/10).
- **L3 Sistemas:** Next.js 15 App Router + Supabase Pro + Vercel + Tailwind + shadcn/ui + Biome.
- **WhatsApp:** Evolution API (pilotos 1-2 semanas) → Cloud API directa de Meta o 360dialog (producción).
- **Observabilidad:** Langfuse Cloud Free + Helicone Free + Sentry.
- Detalle completo y por confirmar → `06-Tecnico/Stack_Solemia.md`.

## Setup commands (proyecto L3 nuevo)

```
npx create-next-app -e with-supabase
npx biome migrate eslint --include-inspired
npx @sentry/wizard@latest -i nextjs
pnpm install && pnpm dev
```

## Workflow para features (Spec-Driven Development)

1. Spec en `specs/00X-feature/spec.md` (Problem, User Stories EARS, Acceptance Criteria, Non-Goals).
2. **Plan Mode obligatorio** en Claude Code (Shift+Tab 2x) → output en `specs/00X-feature/plan.md`.
3. Implementar en chunks atómicos ≤300 líneas por commit.
4. **Verify obligatorio**: `pnpm check` antes de cada commit.
5. PR con review automatizado + branch protection (no push directo a `main`).
- Detalle → `06-Tecnico/SDD_Workflow.md`.

## Code style

- TypeScript strict + `noUncheckedIndexedAccess`.
- `app/` solo routing; lógica en `src/lib/` o `src/features/`.
- **Data Access Layer** en `src/lib/dal/` con `import 'server-only'`, `requireUser()`, DTOs mínimos, `cache()` de React.
- Supabase: `lib/supabase/{client,server,middleware}.ts` con `@supabase/ssr` (NO `@supabase/auth-helpers`, deprecated).
- **Server Actions** para mutations de UI; **Route Handlers** solo para webhooks externos.
- shadcn/ui en `components/ui/` — **NO TOCAR** (re-instalaciones del CLI sobrescriben). Wrap en `components/shared/`.
- Variables de entorno: `.env.local` (gitignored), Vercel env vars en prod.
- Detalle → `06-Tecnico/Convenciones_Codigo.md`.

## Reglas críticas (jamás se rompen)

1. **Don't Assume** — pregunta antes de inventar.
2. **Surgical Changes** — cambios mínimos, sin sobre-ingeniería ni abstracciones prematuras.
3. **Preserve What You Don't Understand** — no toques código que no entiendes.
4. **Verify Your Work** — corre `pnpm check` antes de declarar "listo".
5. **Single source of truth** — convenciones viven solo en `bases-de-solemia`.
6. **Base Kit primero** — revisa módulos reutilizables antes de inventar nada nuevo.
7. **Migrations versionadas siempre** — nunca tocar producción a mano.
8. **HITL por defecto** en clientes nuevos (dinero, comunicación pública, decisiones legales).
9. **No `service_role` en frontend ni MCP write expuesto a usuarios** ("lethal trifecta", Willison 2025).
10. **Aprobación escrita antes de cambiar scope** del proyecto.
- Lista completa con justificaciones → `06-Tecnico/Reglas_Anti_Noodle.md`.

## Anti-patterns (NO hacer)

- `getSession()` en server code → usar `getClaims()` o `getUser()`.
- `@supabase/auth-helpers` → deprecated, usar `@supabase/ssr`.
- Push directo a `main`/`master` → siempre PR con review.
- `--no-verify` en commits, `eslint-disable` casual, skip de tests críticos.
- Hardcodear credenciales o `service_role` en código.
- Editar `components/ui/` → wrap en `components/shared/`.
- Pedir a Claude "construye el sistema completo" → chunks atómicos siempre.
- Mezclar workflows de varios clientes en una n8n instance → uno por cliente.
- Evolution API en cliente regulado (banca, salud) → Cloud API + BSP directo.

## Verification commands

- `pnpm typecheck` — TypeScript sin errores.
- `pnpm lint` — Biome con `--max-warnings 0`.
- `pnpm test` — tests críticos (auth, pagos, agendamiento).
- `pnpm build` — build local OK antes de push.
- `npx playwright test` — E2E flujos críticos.
- `supabase db diff` — verificar migrations.

## Más detalle

- Stack completo + costos: `06-Tecnico/Stack_Solemia.md`
- Reglas anti-noodle con explicación: `06-Tecnico/Reglas_Anti_Noodle.md`
- Convenciones de código L3: `06-Tecnico/Convenciones_Codigo.md`
- Arquitecturas: `06-Tecnico/Architecture_L1.md`, `Architecture_L2.md`, `Architecture_L3.md`
- Workflow Spec-Driven: `06-Tecnico/SDD_Workflow.md`
- Bootstrap de repo nuevo: `06-Tecnico/Bootstrap_Repo.md`
