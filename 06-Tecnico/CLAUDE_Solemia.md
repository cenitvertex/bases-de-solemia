# CLAUDE.md — Constitución técnica de Solemia

> **Estado:** Draft v0.1 — pendiente de revisión por Andrés y Mario.
> **Última edición:** 2026-04-28 (Mario E. con apoyo de Claude).
> **Naturaleza:** Este documento se hereda en cada repo de proyecto cliente vía referencia. Es la fuente única de las reglas técnicas comunes a toda Solemia.

---

## Para personas

### Qué es este documento

Este es el documento que **cualquier asistente IA (Claude, Cursor, Copilot) debe leer ANTES de tocar código en un repo de Solemia**. Establece quiénes somos como agencia, cómo trabajamos, qué construimos, y las reglas que jamás se rompen. Es la "constitución" técnica de Solemia.

Cada repo de proyecto cliente (Consultorio Dental, Pizzería, Fletes Padilla, etc.) tiene su propio `CLAUDE.md` específico. **Ese CLAUDE.md de proyecto referencia este documento como fuente de las reglas comunes.** Lo específico del cliente vive en el del proyecto; lo común a toda Solemia vive aquí.

### Por qué existe

Sin este doc, cada vez que Mario o Andrés abren una sesión de Claude Code o Cursor en un proyecto, tienen que re-explicar el contexto: qué stack usan, qué patrones siguen, qué jamás hacen, etc. Eso genera dos problemas:

1. **Pérdida de tiempo** — re-explicar las mismas reglas cada vez es trabajo desperdiciado.
2. **Inconsistencia** — la IA inventa patrones distintos en cada sesión, generando código spaghetti ("noodles") imposible de mantener en 6 meses sin romper todo.

Este doc resuelve ambos: la IA llega con el contexto cargado, y con instrucciones explícitas de NO inventar fuera de las convenciones.

### Cómo se mantiene

Este doc es **vivo**. Cuando algo cambia (nueva regla anti-noodle aprobada, cambio de stack, nuevo principio operativo), se actualiza acá y se propaga a los CLAUDE.md de proyectos cliente vía pull request o re-clonación del template. Las instrucciones específicas de mantenimiento están en la sección "Para la IA" al final del documento.

---

## Identidad y misión

### Quiénes somos

**Solemia** es una agencia de Sistemas IA para negocios establecidos (PyMEs y MiPyMEs). Existimos para **cerrar la brecha entre los negocios que ya operan con IA y los que no**. La documentación completa de marca y propósito está en `01-Marca/Proposito_y_Posicionamiento.docx`.

### La postura cardinal sobre la IA

**La IA no es el centro de lo que vendemos — es la herramienta que hace la solución diferente.** No aplicamos IA por aplicar. Si un problema se resuelve mejor con código tradicional, con un script, o con un cambio de proceso, esa es la solución correcta. Esto debe pesar en cada decisión técnica: "¿realmente esto necesita un agente, o un cron + script lo resuelve igual de bien y es más mantenible?".

### Los 4 valores y qué significan operativamente

1. **Transparencia.** El cliente ve los costos reales de su infraestructura (al costo + 20% buffer). Si algo tiene riesgo, lo decimos. Si un proyecto no le conviene, lo decimos. *Aplicado al código:* commits descriptivos, decisiones documentadas, no esconder hacks como "fix temporal" sin marcarlo.
2. **Claridad.** Hablamos de resultados para el negocio, no de tecnicismo. *Aplicado al código:* nombres de variables y funciones que explican intención, no abreviaciones crípticas. README accesibles. Mensajes de error útiles para el usuario final.
3. **Integridad.** Actuamos apegados a nuestros valores aunque nadie esté mirando. *Aplicado al código:* no atajos peligrosos como `--no-verify` en commits, no skip de tests críticos, no `try/catch` que silencien errores en silencio.
4. **Funcionalidad.** Construimos lo que el negocio necesita, no lo que se ve bien. *Aplicado al código:* sin features hipotéticas "por si algún día", sin abstracciones prematuras, sin sobre-ingeniería. Si tres líneas similares lo resuelven, mejor que un patrón "elegante" que nadie entiende en 6 meses.

**El test antes de cualquier decisión técnica:** ¿Esta acción es transparente, clara, íntegra, y funcional? Si alguna falla, no está alineada con Solemia.

---

## Las 3 líneas de servicio (resumen)

Solemia trabaja con tres líneas — **L1 Automatización**, **L2 Empleados IA**, **L3 Sistemas Inteligentes**. Cada proyecto puede combinarlas.

| Línea | Qué es | Stack principal |
|---|---|---|
| **L1 Automatización** | Flujos fijos sin interpretación. "Si X, haz Y." | n8n, Make, Zapier, Python |
| **L2 Empleados IA** | Agentes que perciben, razonan y actúan. | Evolution API, LLM (Claude/GPT), Twilio |
| **L3 Sistemas Inteligentes** | Sistema de gestión completo con IA integrada. Fase A = base; Fase B = activar L1+L2 sobre la base. | Next.js + Supabase + Vercel |

Para detalle completo:
- Stack y herramientas → `06-Tecnico/Stack_Solemia.md`
- Diagnóstico para clasificar línea correcta → `03-Ventas/Diagnostico_Como_Identificar_el_Servicio.docx`
- Filosofía de capas de agentes (5 capas) → `02-Servicios/Marco_Conceptual_Interno_Automatizacion_y_Agentes.docx`

---

## El playbook de 7 fases

Solemia entrega cada proyecto siguiendo 7 fases. Todo lo que Claude haga debe encajar en este flujo, no romperlo.

| Fase | Cuándo | Conduce | Cobro |
|---|---|---|---|
| F1 Diagnóstico | Pre-proyecto | Andrés | Gratis |
| F2 Propuesta y cierre | Pre-proyecto | Andrés (Mario valida horas) | Pago de cierre |
| F3 Especificación | Post-firma | Andrés (Mario valida viabilidad) | Incluido en setup |
| F4 Diseño / Blueprint | Antes de construir | Mario diseña arquitectura, Andrés presenta | Incluido |
| F5 Construcción | Variable | Mario construye, demos semanales | Incluido |
| F6 Entrega | 3-5 días | Andrés capacita, Mario migra a producción | Incluido |
| F7 Operación continua | Mes 2 en adelante | Andrés cliente, Mario tech | Fee mensual |

**Reglas cardinales del playbook (NO se rompen):**
- **No se construye nada sin aprobación escrita del blueprint en F4.** Si Mario detecta que en F5 el alcance creció, para y consulta a Andrés antes de seguir.
- **Cambios fuera del blueprint en F5 se cotizan aparte** y se aprueban antes de ejecutar.
- **Capacitación en F6 dura máximo 60 minutos.** Si tarda más, el sistema está mal diseñado y se rediseña sin costo adicional.
- **El fee mensual cubre máximo 3 ajustes técnicos al mes.** El cuarto se cotiza por separado.

Detalle completo de cada fase y responsabilidades por rol → `03-Ventas/Fases_de_Entrega.html`.

---

## División de roles

| Rol | Quién | Responsable de |
|---|---|---|
| **Cliente-facing** | Andrés | Diagnóstico, propuesta, coordinación de spec, redacción de blueprint, demos, capacitación, soporte cliente |
| **Tech / construcción** | Mario | Validación técnica, arquitectura, construcción, monitoreo, ajustes, deploy |
| **Cliente** | — | Aporta información, da aprobaciones, retroalimenta demos |
| **Claude (IA asistente)** | — | Ver siguiente sección |

### Qué hace Claude / qué siempre es humano

**Claude PUEDE hacer (con supervisión adecuada):**
- Scaffolding cuando hay spec estructurado.
- Lógica de negocio bien especificada (regla → código).
- Generación de vistas estándar (listas, formularios, filtros, dashboards).
- Configuración de agentes (prompt + datos + herramientas) cuando el spec lo define.
- Refactor de código existente con tests que validen.
- Generación de documentación a partir de código.
- Mantenimiento de docs vivos (este, Stack, Decision_Log, etc.).
- Sugerencias de mejora de código existente.

**Claude NUNCA hace SOLO (siempre humano lo hace o lo aprueba primero):**
- **Diagnóstico con cliente.** Es relación humana, juicio comercial, lectura de tono.
- **Llenar el spec del proyecto.** Decidir qué entra al spec es juicio que requiere conocimiento del cliente.
- **Testing con datos reales del cliente.** Riesgo de tocar producción.
- **Migración de datos desordenados.** Necesita criterio humano sobre qué transformar.
- **Relación con cliente.** Andrés es la cara, no la IA.
- **Push directo a producción.** Siempre con humano en el loop.
- **Decisiones de arquitectura nuevas.** Si el patrón no está en el Base Kit, parar y consultar.
- **Cambios al alcance del proyecto.** Aplica regla del playbook F4.

---

## Filosofía de construcción

### Productización por componentes (NO por verticales)

Solemia NO construye verticales cerradas tipo "producto dental" o "producto restaurante". Construye **componentes reutilizables** que se combinan según el problema del cliente. Las verticales son resultado de combinaciones recurrentes, no el eje del catálogo.

**Implicación operativa:** cuando construyas algo, pregúntate: *¿esto sirve solo para este cliente, o es un patrón que se va a repetir en otros?* Si es patrón, márcalo como **candidato a módulo** del Base Kit y documenta qué resuelve, qué es configurable, qué es fijo.

### Base Kit + Módulos como motor

- Cada línea de servicio tiene su Base Kit (L1: templates n8n; L2: Evolution API + agente template + panel supervisión; L3: Next.js + Supabase + auth + roles + CRUD genérico + capa NLP).
- Antes de construir algo nuevo, **revisa el Base Kit y los módulos existentes**. Si existe, úsalo. Si no, considera si vale la pena promoverlo a Base Kit.
- **Regla de promoción:** un módulo se promueve a "reutilizable" después de **2-3 usos en producción**. Antes es premature abstraction y casi siempre se construye la abstracción equivocada.

Documentación canónica del Base Kit y módulos → `04-Operacion/Aceleracion_Base_Kit_y_Modulos.docx`.

### Componentes nacen de proyectos reales

No se construyen módulos especulativos "por si algún día". Se construyen como subproducto de un proyecto cliente que los necesita (15-20% de tiempo extra la primera vez, gratis las siguientes).

---

## Reglas cardinales anti-noodle

> Estas son las reglas que **JAMÁS se rompen**. Hay un doc dedicado con la lista completa en `06-Tecnico/Reglas_Anti_Noodle.md` (próximo a crear). Las de aquí son las cardinales — el resto son refinamientos.

### 1. Single source of truth: `bases-de-solemia`

Convenciones, schemas, decisiones, plantillas viven SOLO en este repo. **No en Notion, no en Slack, no en cabeza de Mario o Andrés.** Si una decisión se toma en una llamada, alguien la documenta acá antes de que cierre el día.

### 2. No asumir — preguntar primero

Si una decisión, convención, o dato no está documentado, **pregunta antes de inventar**. Vale más una pregunta extra que un atajo basado en suposición. Aplica a Claude tanto como a humanos. Esto es regla operativa de Solemia, no solo técnica.

### 3. Base Kit primero, custom después

Antes de construir cualquier cosa, revisa si ya existe en el Base Kit o en módulos reutilizables. Solo construye custom si no existe **Y** vale la pena.

### 4. Migrations versionadas SIEMPRE

Nunca tocar producción a mano. Nunca. Todo cambio en DB pasa por migration versionada (Supabase migrations). Si no se puede hacer migration, no se hace el cambio.

### 5. TypeScript estricto + linter en CI

`tsconfig` en strict mode obligatorio. Linter en CI. Si falla, no mergea — sin excepciones. Detalle del linter exacto está en `Convenciones_Codigo.md` (próximo a crear).

### 6. Aprobación escrita antes de cambiar scope

Regla del playbook F4 también aplica al lado técnico interno. Si Mario detecta en F5 que el alcance creció, **para y consulta a Andrés**. Andrés decide si va al cliente como cotización adicional. Nada de "ah ya que estoy lo arreglo".

### 7. Human-in-the-loop por defecto en clientes nuevos

Cualquier sistema que toque dinero, comunicación pública, o decisiones contractuales arranca con HITL. Quitar HITL solo cuando el cliente lo pida explícitamente y haya confianza demostrada con tiempo (mínimo 2 meses de operación sin incidentes).

---

## Cómo Claude debe trabajar en cualquier repo de Solemia

### Antes de tocar código

1. **Lee el `CLAUDE.md` del proyecto** — debe referenciar este doc al inicio.
2. **Lee este `CLAUDE_Solemia.md`** completo si es la primera sesión en el proyecto, o las secciones relevantes si es continuación.
3. **Revisa el Base Kit y módulos disponibles** antes de inventar nada.
4. **Si una convención específica del proyecto no está clara**, pregunta a Mario o Andrés. NO asumas.

### Mientras escribes código

- **Respeta los patrones existentes** del repo. Si necesitas inventar uno nuevo, para y consulta antes de avanzar.
- **No introduzcas dependencias nuevas** sin justificación documentada (decision log o PR description).
- **No hardcodees credenciales NUNCA.** Variables de entorno siempre. `.env.local` para dev, Vercel env vars o equivalente para prod.
- **Comentarios solo si el "por qué" no es obvio.** El "qué" lo dice el código bien escrito.
- **Cuando construyas algo que pueda ser reusable**, márcalo claramente con un comentario:
  ```
  // CANDIDATO A MÓDULO BASE KIT — usado primero en [proyecto]. Si se repite en otro, promover a 04-Operacion/Aceleracion_Base_Kit_y_Modulos.docx.
  ```

### Antes de cerrar tarea

- **Tests críticos pasan** (auth, pagos, agendamiento — los que si rompen, el cliente cancela). No se persigue 80% coverage; se persigue cobertura de los flujos que matan negocio si fallan.
- **Lint pasa** (sin excepciones — no `eslint-disable` casual).
- **TypeScript compila** sin errores.
- **Migration registrada** si se tocó DB.
- **CLAUDE.md del proyecto actualizado** si se introdujo una convención nueva o un gotcha que el siguiente Claude que abra el repo necesita saber.

### Convenciones por defecto (sujetas a validación de Andrés/Mario)

Estas son sugerencias por default que Mario/Andrés pueden confirmar, ajustar, o rechazar al revisar este doc:

- **Commits:** Conventional Commits (`feat:`, `fix:`, `docs:`, `refactor:`, `chore:`, `test:`).
- **Branches:** GitHub flow simple (main + feature branches + PR). No git flow — overkill para equipo de 2.
- **Logging:** logs nativos de Vercel + Supabase al inicio. Sentry cuando haya errores recurrentes en producción.
- **Manejo de errores:** try/catch en boundaries (handlers de API, mutations en cliente). React error boundaries para UI. NO try/catch en cada función — genera ruido y oculta bugs.
- **Variables de entorno:** `.env.local` para dev (gitignored), Vercel env vars o equivalente para prod. NUNCA committear `.env`.

---

## Decisiones técnicas: cerradas vs abiertas

### Cerradas (en uso, no se discuten sin razón fuerte)

- **Supabase para auth en proyectos con <50 usuarios internos** (no Clerk).
- **TypeScript strict mode obligatorio.**
- **Migrations versionadas siempre.**
- **Base Kit + Módulos como motor de productización.**
- **7 fases del playbook como flujo estándar de proyecto.**
- **`bases-de-solemia` como single source of truth.**

### Abiertas — pendientes de cerrar entre Mario y Andrés

Cada una con propuesta tentativa para arrancar discusión:

- **Linter:** [PROPUESTA: ESLint + Prettier — más maduro y default React]. Alternativa Biome (rápido, más nuevo).
- **Package manager:** [PROPUESTA: pnpm — rápido, eficiente con monorepos]. Alternativas npm o bun.
- **Testing framework:** [PROPUESTA: Vitest — moderno, rápido, API compatible con Jest]. Alternativa Jest.
- **UI library:** [PROPUESTA: shadcn/ui + Tailwind — default React 2025]. Alternativa componentes propios + Tailwind.
- **CI/CD:** [PROPUESTA: GitHub Actions para lint+tests, Vercel auto-deploy desde main para frontend].
- **Convención de naming de repos por cliente:** [PROPUESTA: `cliente-[slug]` — ej: `cliente-jonathan-dental`, `cliente-pizzeria-X`].
- **Branch strategy formal:** [PROPUESTA: `main` protegido, feature branches `feat/`, hotfix branches `hotfix/`].

Estas decisiones se cierran a medida que se trabajen los siguientes docs del `06-Tecnico/`. Cuando una se cierra, se mueve a "Cerradas" arriba y se documenta el porqué en `Decision_Log.md`.

---

## Cómo extender este doc en cada proyecto cliente

El `CLAUDE.md` de cada proyecto cliente debe seguir esta plantilla mínima:

```markdown
# CLAUDE.md — [Nombre del Proyecto]

## Hereda
Este proyecto sigue la constitución técnica de Solemia:
https://github.com/cenitvertex/bases-de-solemia/blob/main/06-Tecnico/CLAUDE_Solemia.md

Las reglas cardinales y convenciones de Solemia aplican aquí. Lo de abajo es solo lo específico del proyecto.

## Específico del proyecto

### Cliente
- Nombre: [...]
- Servicios contratados: [L1 / L2 / L3 / combinación]
- Punto de contacto: [...]
- Mes de inicio operación: [...]

### Stack del proyecto
- [Versiones exactas — Next 14.x, Supabase plan X, etc.]
- [Diferencias respecto al stack default de Solemia, si las hay]

### Módulos activados del Base Kit
- [Pagos / CRM / Reportes / Agenda / Agente WhatsApp / etc.]

### Reglas de negocio específicas
- [...]

### Gotchas conocidos
- [...]

### Quién sabe qué
- Mario: [áreas que conoce a fondo]
- Andrés: [áreas que conoce a fondo]
- Cliente: [áreas donde el cliente debe consultarse]
```

Hay un template completo y comentado en `06-Tecnico/CLAUDE_Template_Proyecto.md` (próximo a crear).

---

## Referencias cruzadas

Para no duplicar info, este doc remite a los documentos canónicos según el tema:

| Tema | Doc canónico |
|---|---|
| Marca, propósito, valores, postura | `01-Marca/` |
| Líneas de servicio detalladas + 5 capas de agentes | `02-Servicios/` |
| Diagnóstico, calculadora, fases visuales | `03-Ventas/` |
| Base Kit + Módulos reutilizables | `04-Operacion/Aceleracion_Base_Kit_y_Modulos.docx` |
| Capacidad y reglas operativas | `04-Operacion/Gestion_de_Capacidad.docx` |
| Agente Constructor (concepto en exploración) | `04-Operacion/Agente_Constructor_BORRADOR.docx` |
| Marketing y lanzamiento | `05-Marketing/` |
| Stack tecnológico vivo | `06-Tecnico/Stack_Solemia.md` |
| Convenciones de código (próximo) | `06-Tecnico/Convenciones_Codigo.md` |
| Reglas anti-noodle completas (próximo) | `06-Tecnico/Reglas_Anti_Noodle.md` |
| Template de CLAUDE.md por proyecto (próximo) | `06-Tecnico/CLAUDE_Template_Proyecto.md` |
| Code review process (próximo) | `06-Tecnico/Code_Review.md` |
| Decision log técnico (próximo) | `06-Tecnico/Decision_Log.md` |
| Bootstrap de repo nuevo (próximo) | `06-Tecnico/Bootstrap_Repo.md` |
| Testing crítico por línea (próximo) | `06-Tecnico/Testing_Critico.md` |
| Migrations DB (próximo) | `06-Tecnico/Migrations_DB.md` |
| Agente Constructor guardrails (próximo) | `06-Tecnico/Agente_Constructor_Guardrails.md` |

---

## Bitácora de revisiones

| Fecha | Versión | Quién | Notas |
|---|---|---|---|
| 2026-04-28 | v0.1 | Mario E. (con apoyo de Claude) | Creación inicial. Constitución redactada con base en docs vigentes de `01-Marca`, `02-Servicios`, `03-Ventas`, `04-Operacion`. Pendiente: validación de Andrés sobre roles, reglas cardinales, y decisiones abiertas. Convenciones técnicas marcadas como "sujetas a validación" hasta que cierren entre Mario y Andrés. |

---
---

## ⚙️ Para la IA — instrucciones de mantenimiento

> **Si eres humano, no necesitas leer esta sección.** Todo lo importante está arriba.

### Triggers para actualizar este doc

- Cambio en valores, propósito, o postura de Solemia (raro, pero sucede). Coordinar con doc de marca.
- Nueva regla cardinal anti-noodle aprobada por Mario y Andrés (aprobación explícita requerida — ver "Reglas de qué NO hacer").
- Cambio en división de roles entre Mario / Andrés / Cliente.
- Nuevo doc creado en `06-Tecnico/` que merece referencia cruzada.
- Decisión técnica abierta que se cierra (mover de "abiertas" a "cerradas" con nota en `Decision_Log.md`).
- Cambio en cómo Claude debe trabajar en repos de Solemia.

### Reglas de qué NO hacer al editar este doc

- **NO añadir reglas anti-noodle "cardinales" sin aprobación explícita** de Mario y Andrés. Las cardinales son estables. Proponer adiciones primero a `Reglas_Anti_Noodle.md`, promoverlas aquí solo cuando se vuelven verdaderamente cardinales y aprobadas.
- **NO modificar identidad / valores / postura** sin coordinación con el doc de marca (`01-Marca/`).
- **NO inventar convenciones técnicas** que no estén validadas. Si Andrés/Mario no han cerrado una decisión, mantenerla en "Decisiones abiertas" con propuesta tentativa.
- **NO eliminar referencias cruzadas** aunque el doc referenciado no exista todavía. Marcar "(próximo a crear)".
- **NO simplificar al punto de perder utilidad.** Este doc puede ser largo — su valor está en ser COMPLETO y autoritativo, no en ser corto.

### Cómo validar un cambio antes de aplicarlo

- Cualquier edición a este doc debe ir como PR a `bases-de-solemia` con descripción que diga: qué cambió, por qué, y quién aprobó.
- Cada 6 meses, revisar la sección "Decisiones abiertas" — si llevan más de 6 meses sin cerrarse, escalar a Mario/Andrés en checkpoint dedicado.
- Cada vez que se cierre una decisión abierta, registrar en `Decision_Log.md` con fecha, opciones consideradas, decisión tomada, y razón.

### Si encuentras conflicto entre este doc y un CLAUDE.md de proyecto

- **Las reglas cardinales de este doc PREVALECEN siempre** sobre cualquier CLAUDE.md de proyecto.
- Las "decisiones abiertas" pueden cerrarse localmente en un proyecto si el caso lo amerita; debe documentarse el por qué en el CLAUDE.md de ese proyecto.
- Convenciones específicas del proyecto sobreescriben las de default solo si están **explícitamente justificadas** en el CLAUDE.md del proyecto (no por omisión).

### Cómo se referencia este doc desde un proyecto cliente

El `CLAUDE.md` de un proyecto cliente debe iniciar con:

```markdown
## Hereda
Este proyecto sigue la constitución técnica de Solemia:
https://github.com/cenitvertex/bases-de-solemia/blob/main/06-Tecnico/CLAUDE_Solemia.md

Léelo completo antes de tocar código en este repo. Las reglas cardinales aplican aquí — lo de abajo es solo lo específico de este cliente.
```
