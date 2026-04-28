# Stack de Solemia

> **Estado:** Draft v0.2 — pendiente de revisión por Andrés.
> **Última edición:** 2026-04-28 (Mario E. con apoyo de Claude).

---

## Para personas

Solemia trabaja con tres líneas de servicio — **L1 Automatización**, **L2 Empleados IA**, **L3 Sistemas Inteligentes** — y cada una tiene su propio stack tecnológico. Este documento es el inventario vivo de **toda herramienta que usamos hoy o estamos evaluando**, con el por qué de cada decisión y el estado actual.

Sirve como dos cosas a la vez. Primero, **referencia rápida**: ¿qué hostea qué? ¿qué plan tenemos contratado de Supabase? ¿qué versión de Next.js corre en producción? Segundo, **decision log de stack**: cada herramienta dice por qué se eligió y qué alternativas se consideraron. Esto evita que cada 3 meses repitamos el mismo debate de "¿y por qué no usamos Firebase / Cloudflare / lo de moda?".

### Resumen por línea de servicio

**Para hacer L3 (Sistemas Inteligentes):** la columna vertebral es **Next.js** (frontend), **Supabase** (Postgres + Auth + Storage), y **Vercel** (deploy). Esta combinación es nuestro Base Kit L3 y se clona en cada proyecto cliente nuevo.

**Para hacer L1 (Automatizaciones):** principalmente **n8n** — lo preferimos por su capacidad de self-host y por manejar flujos complejos. **Make** y **Zapier** son respaldo cuando el cliente ya los tiene configurados o cuando ofrecen un conector que n8n no tiene. Cuando un script realmente no encaja en low-code, usamos **Python** directo.

**Para hacer L2 (Empleados IA):** **Evolution API** para la conexión con WhatsApp (self-hosted, no oficial — viene con riesgo de baneo si se viola TOS de WhatsApp, por eso siempre usamos números dedicados, no personales del cliente). **Twilio** cuando se necesita SMS, voz, o WhatsApp oficial. El razonamiento principal lo da **Claude** (Anthropic), que también es la herramienta interna de Mario para construir vía Claude Code.

**Infraestructura transversal:** **Hostinger** para hosting web, **Easypanel** como panel de control para todo lo self-hosted (probablemente n8n, Evolution API, paneles internos).

### Regla de oro

Si una herramienta nueva se va a meter a un proyecto cliente, **primero se da de alta acá**. No después. Esta regla es la que evita que en 6 meses nadie sepa qué corre dónde, qué plan tiene contratado, ni por qué se eligió.

---

## Resumen rápido (tabla)

| Herramienta | Categoría | Línea | Estado | Plan/tier |
|---|---|---|---|---|
| Hostinger | Hosting web/VPS | Transversal | En uso | [PENDIENTE] |
| Easypanel | Orquestador self-hosted | Transversal | En uso | [PENDIENTE] |
| Vercel | Deploy Next.js | L3 | En uso | [PENDIENTE] |
| Supabase | BaaS (Postgres+Auth+Storage) | L3, transversal | En uso | **Free tier** |
| Next.js | Framework frontend | L3 | En uso | [PENDIENTE versión] |
| n8n | Orquestador low-code | L1 | En uso | [PENDIENTE — self / cloud] |
| Make | Orquestador low-code SaaS | L1 | En uso (casos específicos) | [PENDIENTE] |
| Zapier | Orquestador low-code SaaS | L1 | En uso (casos específicos) | [PENDIENTE] |
| Evolution API | API WhatsApp no oficial | L2 | En uso | Self-hosted |
| Twilio | SMS / voz / WhatsApp oficial | L1, L2 | En uso | [PENDIENTE] |
| Claude (Anthropic) | LLM + asistente Claude Code | L2, L3, interno | En uso | [PENDIENTE] |
| GPT (OpenAI) | LLM | L2, L3 | [PENDIENTE confirmar] | [PENDIENTE] |
| Python | Lenguaje (scripts, neuro-simbólico) | L1, L2 | En uso | N/A |

---

## Stack actual — detalle por herramienta

### Hosting & Infraestructura

#### Hostinger
- **Categoría:** hosting (web/VPS)
- **Línea(s) de servicio:** transversal
- **Estado:** en uso
- **Plan/tier actual:** [PENDIENTE confirmar con Mario]
- **Para qué se usa:** [PENDIENTE — ¿landing solemia.com? ¿VPS para self-hosted services? ¿paneles cliente?]
- **Alternativas consideradas:** Vercel (para Next.js específicamente), DigitalOcean, AWS Lightsail
- **Última verificación:** 2026-04-28
- **Notas:** —

#### Easypanel
- **Categoría:** orquestador / panel de control para servicios self-hosted
- **Línea(s) de servicio:** transversal
- **Estado:** en uso
- **Plan/tier actual:** [PENDIENTE]
- **Para qué se usa:** [PENDIENTE — probable: deploy de n8n self-hosted, Evolution API, paneles internos]
- **Alternativas consideradas:** Coolify, Dokku, Portainer
- **Última verificación:** 2026-04-28
- **Notas:** —

#### Vercel
- **Categoría:** hosting / deploy especializado en Next.js
- **Línea(s) de servicio:** L3 (Sistemas Inteligentes)
- **Estado:** en uso
- **Plan/tier actual:** [PENDIENTE — ¿Hobby por proyecto? ¿Pro?]
- **Para qué se usa:** Deploy de los frontends Next.js de los Sistemas Inteligentes L3.
- **Alternativas consideradas:** Netlify, Cloudflare Pages, self-host en Hostinger (descartado por DX)
- **Última verificación:** 2026-04-28
- **Notas:** Parte del Base Kit L3 documentado en `04-Operacion/Aceleracion_Base_Kit_y_Modulos.docx`.

### Backend & Base de Datos

#### Supabase
- **Categoría:** BaaS (Postgres + Auth + Storage + Realtime)
- **Línea(s) de servicio:** L3 principal; transversal cuando L2 requiere auth/db
- **Estado:** en uso
- **Plan/tier actual:** **Free tier** (confirmado 2026-04-28 por Mario)
- **Para qué se usa:** Base de datos Postgres, autenticación, storage, RLS para roles del cliente.
- **Alternativas consideradas:** Clerk (descartado para auth con <50 usuarios internos — ver memoria interna), Firebase, Pocketbase self-hosted
- **Última verificación:** 2026-04-28
- **Notas:** Convención Solemia — usar Supabase Auth en lugar de Clerk para apps con <50 usuarios internos. Vigilar límites del plan Free (500 MB DB, 1 GB storage, 50K MAU) — al pasar de cierta carga, migrar a Pro.

### Frontend

#### Next.js
- **Categoría:** framework frontend (React)
- **Línea(s) de servicio:** L3
- **Estado:** en uso
- **Plan/tier actual:** [PENDIENTE versión exacta — ¿14? ¿15? ¿App Router o Pages Router?]
- **Para qué se usa:** Frontend de Sistemas Inteligentes L3 (paneles, CRUDs, vistas de cliente).
- **Alternativas consideradas:** Remix, SvelteKit, Astro
- **Última verificación:** 2026-04-28
- **Notas:** Parte del Base Kit L3.

### Orquestación de Flujos (L1)

#### n8n
- **Categoría:** orquestador de automatizaciones (low-code)
- **Línea(s) de servicio:** L1
- **Estado:** en uso
- **Plan/tier actual:** [PENDIENTE — ¿self-hosted en Easypanel? ¿n8n Cloud?]
- **Para qué se usa:** Construcción de flujos L1 (automatizaciones predecibles, sin interpretación).
- **Alternativas consideradas:** Make, Zapier, Python directo
- **Última verificación:** 2026-04-28
- **Notas:** Preferido sobre Make/Zapier cuando se necesita lógica más compleja o ejecutar self-hosted.

#### Make (Integromat)
- **Categoría:** orquestador SaaS (low-code)
- **Línea(s) de servicio:** L1
- **Estado:** en uso (casos específicos)
- **Plan/tier actual:** [PENDIENTE]
- **Para qué se usa:** Automatizaciones SaaS-first, integraciones donde n8n no tiene conector directo.
- **Alternativas consideradas:** n8n, Zapier
- **Última verificación:** 2026-04-28
- **Notas:** —

#### Zapier
- **Categoría:** orquestador SaaS (low-code)
- **Línea(s) de servicio:** L1
- **Estado:** en uso (casos específicos)
- **Plan/tier actual:** [PENDIENTE]
- **Para qué se usa:** Cuando el cliente ya tiene Zapier configurado o cuando se requiere un conector que solo Zapier ofrece.
- **Alternativas consideradas:** n8n, Make
- **Última verificación:** 2026-04-28
- **Notas:** —

### Mensajería & Comunicación

#### Evolution API
- **Categoría:** API no oficial de WhatsApp (self-hosted)
- **Línea(s) de servicio:** L2 (Empleados IA con WhatsApp)
- **Estado:** en uso
- **Plan/tier actual:** self-hosted (probable: vía Easypanel — confirmar)
- **Para qué se usa:** Conexión bidireccional con WhatsApp para los Empleados IA L2.
- **Alternativas consideradas:** WhatsApp Business API oficial (más cara, más reglas de uso), Twilio WhatsApp
- **Última verificación:** 2026-04-28
- **Notas:** API NO oficial. Riesgo de baneo de número si se viola TOS de WhatsApp. Usar números dedicados, NO personales del cliente.

#### Twilio
- **Categoría:** API de mensajería (SMS, voz, WhatsApp oficial)
- **Línea(s) de servicio:** L1, L2 (cuando se requiere SMS, voz, o WhatsApp oficial)
- **Estado:** en uso
- **Plan/tier actual:** [PENDIENTE — probable: pay-as-you-go]
- **Para qué se usa:** [PENDIENTE — probable: agentes de voz para Pizzería, fallback SMS, WhatsApp oficial cuando aplica]
- **Alternativas consideradas:** Vonage, MessageBird, AWS SNS (solo SMS)
- **Última verificación:** 2026-04-28
- **Notas:** —

### LLMs

#### Claude (Anthropic)
- **Categoría:** LLM
- **Línea(s) de servicio:** L2, L3 + uso interno (Claude Code para construcción de Mario)
- **Estado:** en uso
- **Plan/tier actual:** [PENDIENTE — ¿API directa? ¿Pro? ¿Claude Code para Mario?]
- **Para qué se usa:** Razonamiento principal de agentes L2; asistente de construcción para Mario (Claude Code).
- **Alternativas consideradas:** GPT-4 / GPT-4o (OpenAI), Gemini
- **Última verificación:** 2026-04-28
- **Notas:** —

#### GPT (OpenAI)
- **Categoría:** LLM
- **Línea(s) de servicio:** L2, L3
- **Estado:** [PENDIENTE confirmar — ¿en uso en algún proyecto o solo evaluado?]
- **Plan/tier actual:** [PENDIENTE]
- **Para qué se usa:** [PENDIENTE]
- **Alternativas consideradas:** Claude, Gemini
- **Última verificación:** 2026-04-28
- **Notas:** —

### Lenguajes & Runtime

#### Python
- **Categoría:** lenguaje (scripts, integraciones, componentes neuro-simbólicos)
- **Línea(s) de servicio:** L1 (scripts), L2 (componentes que ejecutan código duro)
- **Estado:** en uso
- **Plan/tier actual:** N/A
- **Para qué se usa:** Scripts custom donde n8n no alcanza, componentes neuro-simbólicos (cálculos exactos donde la IA no puede equivocarse), integraciones con APIs sin conector.
- **Alternativas consideradas:** Node.js
- **Última verificación:** 2026-04-28
- **Notas:** —

---

## Por confirmar / por llenar

Sección para que Mario y Andrés completen. Cada hueco es una pregunta abierta:

- **Versionado / repos:** ¿Solo GitHub `cenitvertex`? ¿Algún repo en GitLab? ¿Convención de nombres por proyecto cliente?
- **Linter / Formatter:** ¿ESLint? ¿Prettier? ¿Biome? ¿Configuración compartida entre proyectos?
- **Package manager:** ¿npm? ¿pnpm? ¿yarn? ¿bun?
- **Testing framework:** ¿Vitest? ¿Jest? ¿Playwright para E2E? ¿Se hacen tests hoy?
- **CI/CD:** ¿GitHub Actions? ¿Vercel auto-deploy desde main? ¿Hay alguna pipeline definida?
- **Monitoring / Logs / Errores:** ¿Sentry? ¿Logtail? ¿Solo logs nativos de Vercel/Supabase?
- **Analytics de uso:** ¿Posthog? ¿Plausible? ¿Algún tracking en los Sistemas Inteligentes?
- **UI library:** ¿shadcn/ui? ¿Tailwind solo? ¿Componentes propios? ¿Algún design system?
- **Email transaccional:** ¿Resend? ¿SendGrid? ¿AWS SES? ¿Nodemailer directo?
- **Pagos:** ¿Stripe? ¿Mercado Pago? ¿Conekta? ¿Algún gateway local México?
- **Calendar:** ¿Google Calendar API? ¿Cal.com? ¿integración custom?
- **Storage de archivos:** ¿Supabase Storage? ¿S3? ¿Cloudinary?
- **PDFs / generación de documentos:** ¿Alguna librería ya usada?
- **Documentación cliente:** ¿Algo tipo Mintlify, Docusaurus, o todo en Notion?

---

## Deprecadas

(vacío por ahora — entradas que dejen de usarse vienen aquí con razón y fecha de deprecación)

---

## Bitácora de revisiones completas

| Fecha | Quién | Notas |
|-------|-------|-------|
| 2026-04-28 | Mario E. (con apoyo de Claude) | Creación inicial v0.1. Stack base capturado, huecos PENDIENTE pendientes de Mario/Andrés. |
| 2026-04-28 | Mario E. (con apoyo de Claude) | Rewrite v0.2 — sección humana mejorada (narrativa por línea de servicio + tabla resumen). Supabase confirmado Free tier. Sección IA movida al footer del doc. |

---
---

## ⚙️ Para la IA — instrucciones de mantenimiento

> **Esta sección la lee Claude (o cualquier asistente IA) cuando se le pida actualizar este documento.** Si eres un humano, no necesitas leerla — todo lo que necesitas saber está arriba.

**Triggers para actualizar este doc:**
- Se añade una herramienta nueva al stack (incluso si solo está en evaluación).
- Una herramienta cambia de estado (`en evaluación` → `en uso`, o `en uso` → `deprecada`).
- Se cambia de plan/tier (ej: Supabase Free → Pro).
- Aparece una alternativa nueva relevante que vale la pena documentar aunque no se adopte aún.
- Cada 3 meses, validar entradas en estado `en uso` y actualizar `Última verificación`.

**Schema obligatorio por entrada:**
```
### [Nombre de la herramienta]
- **Categoría:** [hosting / db / framework / orquestador / LLM / mensajería / lenguaje / etc.]
- **Línea(s) de servicio:** [L1 / L2 / L3 / transversal]
- **Estado:** [en uso / en evaluación / deprecada]
- **Plan/tier actual:** [free / pro / self-hosted / pay-as-you-go / etc.]
- **Para qué se usa:** [1-2 líneas concretas, sin marketing]
- **Alternativas consideradas:** [lista breve, con razón de descarte de cada una]
- **Última verificación:** [YYYY-MM-DD]
- **Notas:** [opcional — gotchas, límites de plan, costos, dependencias]
```

**Cuando agregues una herramienta nueva:**
1. Añade su ficha completa en la sección "Stack actual — detalle por herramienta", bajo la categoría correcta.
2. Añade fila correspondiente en la tabla de "Resumen rápido".
3. Si introduce cambios sobre el resumen narrativo de "Para personas", actualiza ese párrafo también.
4. Añade entrada en la "Bitácora de revisiones completas" con fecha + qué cambió.

**Reglas de qué NO hacer:**
- **No borrar entradas.** Si una herramienta se deprecata, marcar `Estado: deprecada`, mover a sección "Deprecadas" al final, y añadir nota con la razón. NUNCA eliminar.
- **No editar la justificación original** de una decisión sin añadir entrada al `Decision_Log.md` explicando qué cambió y por qué.
- **No añadir herramientas hipotéticas.** Si no está en uso o en evaluación activa por alguien con fecha definida, no entra.
- **No inventar valores.** Si un campo no se conoce, escribir `[PENDIENTE confirmar con Mario/Andrés]` — nunca poner algo plausible para "completar". Aplica la regla Solemia: no asumir, preguntar primero.

**Cómo validar:**
- Antes de marcar `Última verificación` con fecha de hoy, confirmar contra realidad: revisar que el plan/tier sigue siendo el mismo, que la versión declarada coincide con la que corre.
- Si encuentras un conflicto entre lo que dice el doc y lo que está en producción, **pregunta a Mario o Andrés antes de cambiar**.
