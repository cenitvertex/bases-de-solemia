# Stack de Solemia

> **Estado:** Draft v0.3 — actualizado con investigación de Andrés (3 deep research reports + briefing del Framework de Desarrollo). Pendiente revisión final por Andrés.
> **Última edición:** 2026-04-28 (Mario E. con apoyo de Claude).

---

## Para personas

Solemia trabaja con tres líneas de servicio — **L1 Automatización**, **L2 Empleados IA**, **L3 Sistemas Inteligentes** — y cada una tiene su propio stack tecnológico. Este documento es el inventario vivo de **toda herramienta que usamos hoy o estamos evaluando**, con el por qué de cada decisión y el estado actual.

Sirve como dos cosas: **referencia rápida** (qué hostea qué, qué plan tenemos contratado, qué versión corre) y **decision log de stack** (cada herramienta dice por qué se eligió y qué alternativas se consideraron). Esto evita que cada 3 meses repitamos el mismo debate de "¿y por qué no usamos X?".

### Resumen por línea de servicio

**Para hacer L3 (Sistemas Inteligentes):** **Next.js 15 App Router** (frontend) + **Supabase Pro** (Postgres + Auth + Storage + Realtime + pgvector) + **Vercel** (deploy) + **Tailwind + shadcn/ui** (UI) + **Biome** (lint+format). Esta combinación es nuestro Base Kit L3 — viene del template oficial `npx create-next-app -e with-supabase`.

**Para hacer L1 (Automatizaciones):** principalmente **n8n self-hosted** (Postgres + queue mode + Redis) sobre VPS. **Make** y **Zapier** son respaldo para casos donde el cliente ya los tiene configurados o necesitan un conector que solo ellos ofrecen. **Python** cuando un script realmente no encaja en low-code.

**Para hacer L2 (Empleados IA):** patrón "**brain + adapters**". **Brain** en Next.js usando **Vercel AI SDK 6** (ToolLoopAgent, structured outputs, MCP nativo) — vive en el mismo repo que el L3 cuando aplica. **Adapters** por canal (WhatsApp / web chat / email / voz). Modelos vía **Helicone Gateway** (0% markup) con **routing 70/20/10**: Claude Haiku 4.5 (default, 70%) → Claude Sonnet 4.6 (tool calling intensivo, 20%) → Opus o GPT-5.4 (10% casos delicados). **n8n se usa SOLO como bus de side-effects** del agente (cron, integraciones SaaS), nunca como el loop de decisión.

**WhatsApp:** **Evolution API** self-hosted SOLO para pilotos de 1-2 semanas (riesgo documentado de bans en 2-8 semanas con uso real). Para producción: **WhatsApp Cloud API directa de Meta** (embedded signup) o **360dialog** ($25-49/mes/número, 0% markup). **NUNCA Twilio para WhatsApp en LATAM** — su markup de $0.005/msg destruye margen. Cambio importante de Meta julio 2025: mensajes de servicio (cliente inicia, dentro de 24h window) son **gratis**.

**Infraestructura transversal:** Hosting web ligero (Hostinger por ahora, evaluar). Para servicios self-hosted (n8n, Evolution): **Hetzner CCX13 (€14/mes)** con **Coolify** como panel de control es la opción 5-10× más rentable según investigación de Andrés (vs Easypanel actual — pendiente decidir migración).

**Observabilidad y guardrails:** **Sentry** (errors + Session Replay 10%), **Langfuse Cloud Free** (50K obs/mes para LLM tracing), **Helicone** (LLM gateway + cost monitoring), **Promptfoo** (evals pre-deploy en CI). Code review automatizado con **CodeRabbit** ($24/user/mes) o **Greptile** ($30/dev/mes para proyectos $50k+).

**Memoria de agentes (L2):** **pgvector + HNSW en Supabase** (incluido en Pro $25/mes) cubre hasta 1M vectores con accuracy ≥99%. Mem0 / Zep solo cuando cliente paga retainer >$10k MXN/mes.

### Regla de oro

Si una herramienta nueva se va a meter a un proyecto cliente, **primero se da de alta acá**. No después. Esta regla evita que en 6 meses nadie sepa qué corre dónde, qué plan tiene contratado, ni por qué se eligió.

---

## Resumen rápido (tabla)

| Herramienta | Categoría | Línea | Estado | Plan/tier |
|---|---|---|---|---|
| **Hostinger** | Hosting web ligero | Transversal | En uso (evaluar contra Hetzner) | [PENDIENTE] |
| **Easypanel** | Panel self-hosted | Transversal | En uso (evaluar contra Coolify) | [PENDIENTE] |
| **Hetzner** | VPS para self-host | L1, L2 infra | Propuesta investigación Andrés | CCX13 €14/mes propuesto |
| **Coolify** | Panel orquestación self-host | Transversal | Propuesta investigación Andrés | OSS gratis |
| **Vercel** | Deploy Next.js + AI Gateway | L3 | En uso | [PENDIENTE — Hobby/Pro] |
| **Supabase** | BaaS (Postgres+Auth+Storage+Realtime+pgvector) | L3, transversal | En uso | **Free tier** (migrar a Pro $25 al primer cliente real) |
| **Next.js** | Framework frontend (React) | L3 | En uso | **15 App Router** recomendado |
| **Tailwind** | CSS utility-first | L3 | En uso por default Next | — |
| **shadcn/ui** | UI components | L3 | En uso por default Next | OSS, **NO editar** archivos en `components/ui/` |
| **Biome** | Linter + formatter | L3 | Propuesta (reemplaza ESLint+Prettier, 10-25× más rápido) | OSS |
| **Husky + lint-staged** | Pre-commit hooks | L3 | Propuesta | OSS |
| **Vercel AI SDK** | Framework para agentes IA | L2 | Propuesta investigación Andrés | OSS, v6 (madurez total 2026) |
| **n8n** | Orquestador low-code (L1) + side-effects (L2) | L1, L2 | En uso | Community self-hosted (registro email gratis desbloquea folders) |
| **Make (Integromat)** | Orquestador SaaS | L1 | En uso (casos específicos) | [PENDIENTE] |
| **Zapier** | Orquestador SaaS | L1 | En uso (casos específicos) | [PENDIENTE] |
| **Evolution API** | WhatsApp no oficial | L2 (pilotos) | En uso, **solo pilotos 1-2 semanas** | Self-hosted |
| **WhatsApp Cloud API** | WhatsApp oficial directa Meta | L2 (producción) | Propuesta producción | Pago por mensaje (servicio gratis dentro 24h) |
| **360dialog** | BSP de WhatsApp | L2 (alternativa Cloud API) | Propuesta producción | $25-49/mes/número, **0% markup** |
| **Twilio** | SMS / voz / WhatsApp | L2 voz, NO WhatsApp en LATAM | En uso (solo voz cuando aplique) | Pay-as-you-go |
| **Claude (Anthropic)** | LLM principal + Claude Code | L2, L3, uso interno | En uso | [PENDIENTE — Pro/Max 5x para Mario] |
| **OpenAI GPT** | LLM secundario | L2, L3 | [PENDIENTE confirmar uso real] | [PENDIENTE] |
| **Gemini (Google)** | LLM (extracción ultra barata) | L2 | Propuesta investigación Andrés | Gemini 2.5 Flash-Lite $0.10/$0.40 |
| **Helicone** | LLM Gateway (0% markup, observabilidad) | L2 transversal | Propuesta | Free tier (10K req/mes) |
| **Langfuse** | LLM tracing + evals + prompt management | L2 | Propuesta | Cloud Free 50K obs/mes |
| **Promptfoo** | LLM evals pre-deploy | L2 | Propuesta | OSS gratis |
| **Sentry** | Errors + Session Replay + perf | L3 | Propuesta investigación Andrés | Developer $26/mes |
| **CodeRabbit** | PR review automatizado | L3 | Propuesta | $24/user/mes |
| **Cursor Pro** | IDE secundaria + autocomplete | Tooling Mario | Propuesta investigación Andrés | $20/mes/dev |
| **Antigravity (Google)** | IDE alternativa con Gemini 3 Pro | Tooling Mario | En uso (preview público) | Free preview |
| **MCP servers** | Context para Claude/Cursor | Tooling | Propuesta: Context7, Supabase MCP, GitHub MCP, Playwright MCP, Sentry MCP | OSS / OAuth |
| **Pgvector** | Memoria semántica HNSW | L2 | Incluido en Supabase | OSS extension |
| **Backblaze B2** | Backups offsite | Infra transversal | Propuesta | $0.006/GB sin egress |
| **Python** | Scripts, neuro-simbólico | L1, L2 | En uso | N/A |
| **MercadoPago** | Pagos LATAM | L3 cuando aplique | En uso | [PENDIENTE — comisiones MX] |

---

## Stack actual — detalle por herramienta

### Hosting & Infraestructura

#### Hostinger
- **Categoría:** hosting web ligero
- **Línea(s):** transversal
- **Estado:** en uso (evaluar contra Hetzner para n8n self-hosted)
- **Plan/tier:** [PENDIENTE confirmar]
- **Para qué se usa:** [PENDIENTE — probable: landing solemia.com, hosting general]
- **Alternativas consideradas:** Vercel (Next.js específicamente), Hetzner (mejor para self-host n8n), DigitalOcean
- **Última verificación:** 2026-04-28
- **Notas:** Investigación Andrés sugiere migrar n8n a Hetzner por costo/rendimiento.

#### Easypanel
- **Categoría:** panel de control para self-hosted
- **Línea(s):** transversal
- **Estado:** en uso (evaluar contra Coolify)
- **Plan/tier:** [PENDIENTE]
- **Para qué se usa:** [PENDIENTE — probable: deploy n8n self-hosted, Evolution API, paneles internos]
- **Alternativas consideradas:** Coolify (recomendado por Andrés), Dokku, Portainer
- **Última verificación:** 2026-04-28

#### Hetzner Cloud (PROPUESTO)
- **Categoría:** VPS para self-host
- **Línea(s):** L1, L2 infraestructura
- **Estado:** propuesta investigación Andrés (no en uso aún)
- **Plan/tier:** CCX13 €14/mes (2 vCPU dedicado, 8GB) sostiene main + 2 workers + Redis + Postgres para 5-15 clientes en L1
- **Para qué se usa propuesto:** alojar n8n + Evolution API + Redis + Postgres self-hosted
- **Alternativas consideradas:** mantener Hostinger, DigitalOcean, AWS Lightsail
- **Última verificación:** 2026-04-28
- **Notas:** vCPU dedicado evita "noisy neighbor". 5-10× más rentable que stack Cloud según investigación.

#### Coolify (PROPUESTO)
- **Categoría:** panel orquestación self-host (alternativa Easypanel)
- **Línea(s):** transversal
- **Estado:** propuesta investigación Andrés
- **Plan/tier:** OSS gratis
- **Para qué se usa propuesto:** orquestación de servicios self-hosted (n8n, Evolution, Postgres, Redis) en Hetzner
- **Alternativas consideradas:** Easypanel (actual), Dokku, Portainer
- **Última verificación:** 2026-04-28

#### Vercel
- **Categoría:** hosting/deploy especializado en Next.js + AI Gateway
- **Línea(s):** L3
- **Estado:** en uso
- **Plan/tier:** [PENDIENTE — Hobby por proyecto o Pro $20/seat?]
- **Para qué se usa:** deploy de frontends Next.js de Sistemas L3 + AI Gateway opcional ($5/mes)
- **Alternativas consideradas:** Cloudflare Pages, Netlify, self-host Hostinger
- **Última verificación:** 2026-04-28
- **Notas:** Vercel build Turbo machines (default desde feb 2026) son 9× más caro que Standard — usar Standard para preview deploys, Turbo solo en prod. Hobby NO se puede usar comercialmente (viola TOS).

### Backend & Base de Datos

#### Supabase
- **Categoría:** BaaS (Postgres + Auth + Storage + Realtime + pgvector)
- **Línea(s):** L3 principal, transversal cuando L2 requiere auth/db
- **Estado:** en uso
- **Plan/tier:** **Free tier** (migrar a Pro $25/mes/cliente al arrancar primer proyecto real)
- **Para qué se usa:** Postgres, autenticación, storage, RLS para roles del cliente, memoria semántica con pgvector
- **Alternativas consideradas:** Clerk (descartado para auth con <50 usuarios), Firebase, Pocketbase self-hosted (10 servicios, 2 meses para producción según Stephen Traiforos)
- **Última verificación:** 2026-04-28
- **Notas:** Convención Solemia — Supabase Auth para apps con <50 usuarios internos. Free tier pausa tras 7 días sin tráfico — migrar a Pro al cliente real. Reglas críticas: usar `@supabase/ssr` (NO `@supabase/auth-helpers` deprecated); usar `getClaims()` o `getUser()` en server (NUNCA `getSession()`); pooler obligatorio (puerto 6543 transaction) para n8n→Supabase; cuidado con caracter `@` en password (rompe conexión).

#### Pgvector
- **Categoría:** extensión Postgres para vectores (memoria semántica)
- **Línea(s):** L2 (memoria de agentes con RAG)
- **Estado:** incluido en Supabase
- **Plan/tier:** OSS extension
- **Para qué se usa:** memoria semántica de agentes (HNSW alcanza 99% accuracy vs Qdrant según benchmarks oficiales Supabase, suficiente hasta 1M vectores)
- **Alternativas consideradas:** Pinecone, Qdrant, Weaviate, Mem0 ($249/mes solo si cliente paga >$10k MXN/mes), Zep
- **Última verificación:** 2026-04-28

### Frontend & UI

#### Next.js
- **Categoría:** framework frontend (React)
- **Línea(s):** L3
- **Estado:** en uso
- **Plan/tier:** **15 App Router** recomendado
- **Para qué se usa:** frontend de Sistemas L3 (paneles, CRUDs, vistas, agentes brain)
- **Alternativas consideradas:** Remix, SvelteKit, Astro
- **Última verificación:** 2026-04-28
- **Notas:** Boilerplate inicial: `npx create-next-app -e with-supabase`. Crítico: Next.js 14.2.25+ o 15.2.3+ por CVE-2025-29927 (bypass middleware). NO usar T3 Stack (tRPC duplica Server Actions, Prisma vs supabase-js+RLS introduce fricción).

#### Tailwind CSS
- **Categoría:** CSS utility-first
- **Línea(s):** L3
- **Estado:** en uso por default
- **Plan/tier:** OSS
- **Para qué se usa:** styling de toda la UI L3
- **Última verificación:** 2026-04-28

#### shadcn/ui
- **Categoría:** UI components
- **Línea(s):** L3
- **Estado:** en uso por default
- **Plan/tier:** OSS
- **Para qué se usa:** componentes base de UI (Button, Dialog, Form, etc.)
- **Última verificación:** 2026-04-28
- **Notas:** **NO editar archivos en `components/ui/`** — re-instalaciones del CLI sobrescriben. Para extender, wrap en `components/shared/loading-button.tsx` que envuelva al Button original.

### Calidad de código

#### Biome (PROPUESTO, reemplaza ESLint+Prettier)
- **Categoría:** linter + formatter
- **Línea(s):** L3
- **Estado:** propuesta investigación Andrés
- **Plan/tier:** OSS, v2.x
- **Para qué se usa propuesto:** lint + format en un solo binario, 10-25× más rápido que ESLint+Prettier
- **Alternativas consideradas:** ESLint+Prettier (mantener si dependes de plugins críticos no portados a Biome)
- **Última verificación:** 2026-04-28
- **Notas:** Migración: `npx biome migrate eslint --include-inspired`. Configuración en `biome.json` único.

#### Husky + lint-staged + commitlint (PROPUESTO)
- **Categoría:** pre-commit hooks
- **Línea(s):** L3
- **Estado:** propuesta investigación Andrés
- **Plan/tier:** OSS
- **Para qué se usa propuesto:** correr Biome + tsc --noEmit en archivos staged antes de commit; commitlint para enforcing Conventional Commits
- **Última verificación:** 2026-04-28

### Agentes IA (L2)

#### Vercel AI SDK (PROPUESTO)
- **Categoría:** framework TypeScript para agentes IA
- **Línea(s):** L2 (brain del agente)
- **Estado:** propuesta investigación Andrés
- **Plan/tier:** OSS, v6 (madurez total 2026)
- **Para qué se usa propuesto:** ToolLoopAgent para loops agénticos, structured outputs, MCP nativo, AI Gateway integrado, cambio de proveedor en una línea
- **Alternativas consideradas:** LangChain JS (estorba abstracción), LangGraph (overkill para agencia 2p, $39/seat LangSmith para observabilidad), Mastra (TS-first sobre Vercel AI SDK, considerar cuando crezca complejidad), Pydantic AI (solo Python), AutoGen (en maintenance desde octubre 2025)
- **Última verificación:** 2026-04-28
- **Notas:** Curva 2/5 para alguien con Next.js. Techo: multi-agente con grafos complejos (ahí pierde contra LangGraph, pero ese caso casi nunca aplica a PYME mexicana).

#### Helicone (PROPUESTO — LLM Gateway)
- **Categoría:** LLM gateway con observabilidad
- **Línea(s):** L2 transversal
- **Estado:** propuesta investigación Andrés
- **Plan/tier:** Free tier (10K req/mes); $0 markup hasta 100K req
- **Para qué se usa propuesto:** proxy entre Next.js y proveedores LLM, ahorra cambio de baseURL en una línea, da costo por usuario/cliente, caching automático
- **Alternativas consideradas:** OpenRouter (5-5.5% markup), LiteLLM self-hosted, Vercel AI Gateway ($5/mes si ya estás en Vercel)
- **Última verificación:** 2026-04-28

#### Langfuse (PROPUESTO — LLM observability)
- **Categoría:** LLM tracing + evals + prompt management
- **Línea(s):** L2
- **Estado:** propuesta investigación Andrés
- **Plan/tier:** Cloud Free 50K obs/mes (cubre 3-5 clientes PYME)
- **Para qué se usa propuesto:** tracing de cada llamada LLM, evals automatizados, gestión de prompts versionados
- **Alternativas consideradas:** LangSmith ($39/seat + $2.50-5/1k traces), Phoenix Arize (free 25K spans), Braintrust (enterprise), Helicone (más simple, complementario)
- **Última verificación:** 2026-04-28
- **Notas:** Self-host en Hetzner cuando se exceda Free tier ($10-20 USD/mes infra, MIT, sin caps). NO loguear PII en plain text — usar LLM Guard Anonymize antes.

#### Promptfoo (PROPUESTO — LLM evals)
- **Categoría:** LLM evals pre-deploy
- **Línea(s):** L2
- **Estado:** propuesta investigación Andrés
- **Plan/tier:** OSS gratis (CLI YAML)
- **Para qué se usa propuesto:** comparar prompts/modelos en matrix, red-teaming plugins, golden datasets pre-deploy
- **Alternativas consideradas:** DeepEval (pytest Python), Ragas (RAG-specific)
- **Última verificación:** 2026-04-28

### Orquestación de Flujos (L1)

#### n8n
- **Categoría:** orquestador de automatizaciones (low-code)
- **Línea(s):** L1 principal, L2 (solo bus de side-effects, NO loop de decisión)
- **Estado:** en uso
- **Plan/tier:** Community self-hosted (registro con email gratis desbloquea folders + Debug in Editor + Custom Execution Data)
- **Para qué se usa:** flujos L1, integraciones SaaS, side-effects para agentes L2 (cron, "manda email", "crea factura HubSpot")
- **Alternativas consideradas:** Make, Zapier, Python directo
- **Última verificación:** 2026-04-28
- **Notas:** Para producción: Postgres + queue mode + Redis OBLIGATORIOS (NO SQLite). Source Control nativo n8n Enterprise NO ($667/mes overkill); usar CLI export + Git con `n8n export:workflow --all --separate`. AI Agent node solo Tools Agent desde v1.82.0+. NO mezclar workflows de varios clientes en una instance — folder + tag por cliente.

#### Make (Integromat)
- **Categoría:** orquestador SaaS
- **Línea(s):** L1
- **Estado:** en uso (casos específicos)
- **Plan/tier:** [PENDIENTE]
- **Para qué se usa:** automatizaciones SaaS-first, conectores que n8n no tiene
- **Alternativas consideradas:** n8n, Zapier
- **Última verificación:** 2026-04-28

#### Zapier
- **Categoría:** orquestador SaaS
- **Línea(s):** L1
- **Estado:** en uso (casos específicos)
- **Plan/tier:** [PENDIENTE]
- **Para qué se usa:** cuando cliente ya lo tiene configurado o conector único
- **Alternativas consideradas:** n8n, Make
- **Última verificación:** 2026-04-28

### Mensajería & Comunicación (L2)

#### Evolution API
- **Categoría:** API no oficial WhatsApp (self-hosted)
- **Línea(s):** L2 (solo pilotos)
- **Estado:** en uso, **solo pilotos 1-2 semanas**
- **Plan/tier:** self-hosted (en Easypanel actualmente, evaluar Hetzner+Coolify)
- **Para qué se usa:** demos rápidas (1 día setup) antes de migrar a Cloud API
- **Alternativas consideradas:** WhatsApp Cloud API directa Meta (producción), 360dialog
- **Última verificación:** 2026-04-28
- **Notas:** ⚠️ **Riesgo documentado de bans en 2-8 semanas con uso real** (issues #2228, #2298 en repo oficial nov 2025). Bans masivos a cuentas con 3+ años usando Baileys (issue #1869). Vender Evolution API a una PYME que depende del canal es **negligencia profesional** según investigación Andrés. Estrategia: usar para piloto, migrar a Cloud API/360dialog para go-live (cobrar setup dos veces, evitar el problema). NUNCA Evolution para clientes regulados (banca, salud) — directo a Cloud API + BSP con audit trail.

#### WhatsApp Cloud API directa de Meta (PROPUESTO producción)
- **Categoría:** WhatsApp oficial directa
- **Línea(s):** L2 (producción)
- **Estado:** propuesta producción
- **Plan/tier:** pago por mensaje (servicio gratis dentro de 24h window cliente-iniciado desde julio 2025)
- **Para qué se usa propuesto:** producción de agentes WhatsApp en clientes
- **Alternativas consideradas:** 360dialog (BSP, $25-49/mes/número), Evolution API (solo pilotos)
- **Última verificación:** 2026-04-28
- **Notas:** Cambio Meta julio 2025: per-message pricing, mensajes de servicio (24h window) GRATIS. Marketing templates ~$0.0258 USD/msg México (oct 2025). Embedded signup vía Facebook Business. Desde abril 2026 Meta acepta facturación en MXN. **End-of-life On-Premises API: 23 oct 2025** — solo Cloud API soportada para nuevos números. Frequency cap Meta: ~2 marketing templates/usuario/día sumando todas las marcas.

#### 360dialog (PROPUESTO — BSP)
- **Categoría:** Business Solution Provider de WhatsApp
- **Línea(s):** L2 (producción, alternativa a Cloud API directa)
- **Estado:** propuesta producción
- **Plan/tier:** $25-49/mes/número, **0% markup**
- **Para qué se usa propuesto:** producción cuando cliente no quiere lidiar con verificación Meta directamente
- **Alternativas consideradas:** Cloud API directa Meta, Twilio (NO en LATAM), Wati, Gupshup
- **Última verificación:** 2026-04-28

#### Twilio
- **Categoría:** API mensajería (SMS, voz, WhatsApp)
- **Línea(s):** L2 voz solamente; **NO WhatsApp en LATAM**
- **Estado:** en uso (solo voz cuando aplique)
- **Plan/tier:** pay-as-you-go
- **Para qué se usa:** voz (Twilio Number MX → Vapi/Retell → webhook); SMS en casos puntuales (OTP fallback)
- **Alternativas consideradas:** Vonage, MessageBird (voz); 360dialog/Cloud API (WhatsApp)
- **Última verificación:** 2026-04-28
- **Notas:** Markup $0.005/msg en WhatsApp es absurdo vs 360dialog 0%. Para SMS en LATAM, WhatsApp authentication ($0.024) es más barato. Anti-pattern: apuntar Twilio voz directo a n8n (no responde TwiML válido) — usar Vapi/Retell intermedio.

### LLMs

#### Claude (Anthropic) — default agencia
- **Categoría:** LLM principal + Claude Code asistente
- **Línea(s):** L2, L3, uso interno
- **Estado:** en uso
- **Plan/tier:** [PENDIENTE — Claude Pro? Claude Code Max 5x para Mario ($100/mes)?]
- **Para qué se usa:** razonamiento principal de agentes L2; asistente de construcción Mario (Claude Code)
- **Alternativas consideradas:** GPT (OpenAI), Gemini
- **Última verificación:** 2026-04-28
- **Modelos relevantes (costos USD/1M tokens):**
  - **Haiku 4.5** ($1/$5) — default atención PYME, ~80-120 tokens/s
  - **Sonnet 4.6** ($3/$15) — daily driver agentic, tool calling intensivo
  - **Opus 4.7** ($15/$75) — solo casos delicados, ~10% del tráfico

#### OpenAI GPT
- **Categoría:** LLM secundario
- **Línea(s):** L2, L3
- **Estado:** [PENDIENTE confirmar uso real en proyectos]
- **Plan/tier:** [PENDIENTE]
- **Para qué se usa:** [PENDIENTE]
- **Modelos relevantes:** GPT-4.1-mini ($0.40/$1.60, sweet-spot agencia), GPT-4o ($2.50/$10), GPT-5.4 (nivel Opus)

#### Gemini (Google) (PROPUESTO ultra barato)
- **Categoría:** LLM (extracción ultra barata)
- **Línea(s):** L2 (clasificación, extracción estructurada, RAG masivo)
- **Estado:** propuesta investigación Andrés
- **Plan/tier:** API directa
- **Modelos relevantes:**
  - **Gemini 2.5 Flash-Lite** ($0.10/$0.40) — extracción ultra barata, 1M ctx
  - **Gemini 2.5 Flash** ($0.30/$2.50) — hybrid thinking
  - **Gemini 2.5 Pro** ($1.25/$10) — razonamiento

#### Estrategia de routing 70/20/10 (recomendación Anthropic)

- **70%** queries → Haiku 4.5 o Gemini 2.5 Flash-Lite (clasificación, extracción, FAQ)
- **20%** queries → Sonnet 4.6 (conversación + tool calling intensivo)
- **10%** queries → Opus o GPT-5.4 (cuando un eval del propio Sonnet duda)

Implementación: Text Classifier o `prepareStep` callback en Vercel AI SDK que cambia modelo dinámicamente. Reduce 50-70% del costo total vs todo en Sonnet.

### Tooling de desarrollo

#### Claude Code (Anthropic)
- **Categoría:** asistente de construcción IDE
- **Línea(s):** tooling Mario
- **Estado:** en uso
- **Plan/tier:** [PENDIENTE — Pro o Max 5x ($100/mes)?]
- **Para qué se usa:** construcción asistida por IA, refactor, debugging
- **Alternativas:** Cursor (complementaria, no sustituta), Antigravity (secundaria)

#### Cursor Pro (PROPUESTO)
- **Categoría:** IDE secundaria + autocomplete inline
- **Línea(s):** tooling Mario
- **Estado:** propuesta investigación Andrés
- **Plan/tier:** $20/mes/dev
- **Para qué se usa propuesto:** flujo inline + autocomplete diario (Cursor lidera) en paralelo con Claude Code (que lidera en tareas autónomas largas)
- **Notas:** Total tooling para 2 devs: ~$240/mes (Claude Code Max 5x x2 + Cursor Pro x2). Sobre proyectos $50-75k MXN: <2% del ticket.

#### Antigravity (Google)
- **Categoría:** IDE alternativa con Gemini 3 Pro
- **Línea(s):** tooling Mario
- **Estado:** en uso (preview público desde 18-nov-2025)
- **Plan/tier:** Free preview
- **Para qué se usa:** prototipos visuales rápidos, cross-check con Gemini 3 (#1 en WebDev Arena, 1487 Elo, 76.2% SWE-bench)
- **Notas:** Sigue en preview público — no usar como única IDE en proyectos críticos hasta GA.

#### MCP servers (PROPUESTO — Model Context Protocol)
- **Categoría:** context provider para Claude/Cursor
- **Línea(s):** tooling
- **Estado:** propuesta investigación Andrés
- **Constelación esencial:**
  - **Context7** ([context7.com](https://context7.com)) — fetch de docs versionados al vuelo (Next.js, React, Tailwind, Supabase). Elimina alucinaciones de APIs deprecated.
  - **Supabase MCP** — OAuth dinámico, ya no requiere PAT. Configuración: `claude mcp add --transport http supabase "https://mcp.supabase.com/mcp?project_ref=<ID>&read_only=true"`. **Crítico: nunca conectar a producción**, usar solo dev/staging, mantener `read_only=true` por default.
  - **GitHub MCP** — manejar PRs e issues desde Claude
  - **Playwright MCP** — navegación browser real para verificación visual y E2E generation
  - **Sentry MCP** — triage de errores con stack trace + propose fix

### Calidad: Code review automatizado

#### CodeRabbit (PROPUESTO)
- **Categoría:** PR review automatizado
- **Línea(s):** L3
- **Estado:** propuesta investigación Andrés
- **Plan/tier:** Pro $24/user/mes (free para repos públicos)
- **Para qué se usa propuesto:** review automático de PRs, captura ~44% de bugs con buena relación señal/ruido
- **Alternativas:** Greptile (~82% bugs, $30/dev/mes — agregar en proyectos $50k+), Cursor BugBot (58%, autofix), Sourcery ($10/user/mes)
- **Última verificación:** 2026-04-28

### Observabilidad

#### Sentry (PROPUESTO)
- **Categoría:** errors + Session Replay + performance
- **Línea(s):** L3
- **Estado:** propuesta investigación Andrés
- **Plan/tier:** Developer $26/mes (sobra para 2 personas)
- **Para qué se usa propuesto:** errors en producción, Session Replay 10% sample (game-changer: ves video del usuario causando el error), User Feedback widget, performance traces, tunnelRoute para evitar ad-blockers
- **Instalación:** `npx @sentry/wizard@latest -i nextjs` (30 segundos)
- **Última verificación:** 2026-04-28

### Backups & Storage

#### Backblaze B2 (PROPUESTO)
- **Categoría:** backup offsite
- **Línea(s):** infra transversal
- **Estado:** propuesta investigación Andrés
- **Plan/tier:** $0.006/GB sin egress
- **Para qué se usa propuesto:** backups de Postgres + workflows n8n + volúmenes Docker, sync vía rclone con cifrado GPG AES256
- **Alternativas:** Cloudflare R2 ($0.015/GB sin egress), AWS S3
- **Última verificación:** 2026-04-28

### Lenguajes & Runtime

#### Python
- **Categoría:** lenguaje (scripts, neuro-simbólico)
- **Línea(s):** L1 (scripts), L2 (componentes neuro-simbólicos donde IA no puede equivocarse)
- **Estado:** en uso
- **Plan/tier:** N/A
- **Para qué se usa:** scripts custom donde n8n no alcanza, componentes neuro-simbólicos (cálculos exactos de IVA, totales, fiscales), integraciones con APIs sin conector
- **Alternativas:** Node.js
- **Última verificación:** 2026-04-28

### Pagos

#### MercadoPago
- **Categoría:** pagos LATAM
- **Línea(s):** L3 cuando aplique
- **Estado:** en uso
- **Plan/tier:** [PENDIENTE — comisiones MX]
- **Para qué se usa:** pagos en clientes mexicanos (probablemente)
- **Alternativas:** Stripe (LATAM débil), Conekta, Openpay
- **Última verificación:** 2026-04-28

---

## Por confirmar / por llenar

Sección para que Mario y Andrés completen:

- **Versionado / repos:** ¿Solo GitHub `cenitvertex`? ¿Convención de nombres por proyecto cliente confirmada como `cliente-[slug]`?
- **CI/CD:** ¿Activamos GitHub Actions con jobs paralelos (lint, typecheck, test, build, e2e) + branch protection sin bypass?
- **Email transaccional:** ¿Resend ($20/mes) o Postmark ($15/mes — recomendado por investigación para emails inbound como soporte L1)?
- **Voz IA (cuando aplique L2 voz):** ¿Retell AI o Vapi como motor + Twilio MX para telefonía + ElevenLabs Multilingual v2 (voces Sara/Mateo)?
- **Calendar:** ¿Google Calendar API directa o Cal.com?
- **Documentación cliente:** ¿Mintlify, Docusaurus, o todo en Notion?
- **Decisión Hostinger vs Hetzner** para n8n self-host (impacto en margen de proyectos L1/L2)
- **Decisión Easypanel vs Coolify** (asociado a la decisión anterior)
- **Routing por defecto LLM:** ¿adoptamos 70/20/10 (Haiku/Sonnet/Opus) como default o esperamos al primer L2 real?

---

## Deprecadas

(vacío por ahora — entradas que dejen de usarse vienen aquí con razón y fecha de deprecación)

---

## Bitácora de revisiones completas

| Fecha | Versión | Quién | Notas |
|---|---|---|---|
| 2026-04-28 | v0.1 | Mario E. (con apoyo de Claude) | Creación inicial. Stack base capturado, huecos PENDIENTE. |
| 2026-04-28 | v0.2 | Mario E. (con apoyo de Claude) | Rewrite con sección humana mejorada (narrativa por línea + tabla resumen). Supabase confirmado Free tier. Sección IA al footer. |
| 2026-04-28 | v0.3 | Mario E. (con apoyo de Claude) | Integración con investigación de Andrés (3 deep research reports + briefing Framework de Desarrollo). AÑADE: Vercel AI SDK 6, Biome, Husky+lint-staged, MCPs específicos (Context7, Supabase, GitHub, Playwright, Sentry), Helicone Gateway, Langfuse, Promptfoo, Sentry, CodeRabbit, Cursor Pro, Antigravity, pgvector, Backblaze B2, Hetzner+Coolify (propuesta). AÑADE LLMs específicos con modelos y costos: Haiku 4.5, Sonnet 4.6, Opus 4.7, GPT-4.1-mini, Gemini 2.5 Flash-Lite/Flash/Pro. AÑADE estrategia routing 70/20/10. AÑADE WhatsApp Cloud API + 360dialog como producción. AÑADE patrón "brain + adapters" para L2. ACTUALIZA Evolution API a "solo pilotos" con riesgo documentado. ACTUALIZA n8n con queue mode + Postgres + Redis obligatorios. ACTUALIZA Supabase con reglas críticas (`@supabase/ssr`, `getClaims()`, pooler 6543, etc.). ACTUALIZA Vercel con CVE-2025-29927 + warning Turbo machines preview. |

---
---

## ⚙️ Para la IA — instrucciones de mantenimiento

> Si eres humano, no necesitas leer esta sección — todo lo importante está arriba.

**Triggers para actualizar este doc:**
- Se añade/quita una herramienta del stack (incluso en evaluación).
- Una herramienta cambia de estado (`propuesta` → `en uso`, `en uso` → `deprecada`).
- Cambio de plan/tier (ej: Supabase Free → Pro al primer cliente real).
- Aparece alternativa nueva relevante.
- Cada 3 meses, validar entradas en `en uso`.

**Schema obligatorio por entrada:**
```
### [Nombre]
- **Categoría:** [...]
- **Línea(s):** [L1/L2/L3/transversal/tooling]
- **Estado:** [en uso / propuesta / en evaluación / deprecada]
- **Plan/tier:** [...]
- **Para qué se usa:** [1-2 líneas]
- **Alternativas consideradas:** [con razón de descarte]
- **Última verificación:** [YYYY-MM-DD]
- **Notas:** [opcional — gotchas, costos, dependencias]
```

**Reglas de qué NO hacer:**
- NO borrar entradas — marcar como `deprecada` y mover a "Deprecadas" al final.
- NO inventar valores. Si no se conoce, escribir `[PENDIENTE confirmar con Mario/Andrés]`.
- NO añadir herramientas hipotéticas — solo si están en uso o en evaluación activa con fecha definida.
- NO cambiar la justificación original sin abrir entrada en `Decision_Log.md` explicando qué cambió.

**Cómo validar:**
- Antes de marcar `Última verificación` con fecha de hoy, confirmar contra realidad: revisar plan/tier vigente.
- Si encuentras conflicto entre lo que dice el doc y lo que está en producción, **pregunta a Mario o Andrés antes de cambiar**.

**Sincronización con AGENTS.md:**
- Cambios mayores en stack default deben reflejarse (en versión corta) en `AGENTS.md` sección Stack.
- Si una herramienta pasa de "propuesta" a "en uso" como default, actualizar también AGENTS.md.
