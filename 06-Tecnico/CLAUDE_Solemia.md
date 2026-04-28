# CLAUDE.md — Constitución técnica de Solemia

> **Estado:** Draft v0.2 — pendiente de revisión por Andrés.
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
- **Tests con datos sintéticos, mocks o seeds** — generar tests unitarios o de integración con datos de prueba está OK.
- Sugerencias de mejora de código existente.

**Claude NUNCA hace SOLO (siempre humano lo hace o lo aprueba primero):**
- **Diagnóstico con cliente.** Es relación humana, juicio comercial, lectura de tono.
- **Llenar el spec del proyecto.** Decidir qué entra al spec es juicio que requiere conocimiento del cliente.
- **Testing CON datos reales de producción del cliente.** Riesgo de afectar datos vivos. Si Mario o Andrés quieren un test, le pasan a Claude datos sintéticos / seeds / mocks.
- **Migración de datos desordenados.** Necesita criterio humano sobre qué transformar.
- **Relación con cliente.** Andrés es la cara, no la IA.
- **Push directo a producción.** Siempre con humano en el loop.
- **Decisiones de arquitectura nuevas.** Si el patrón no está en el Base Kit, parar y consultar.
- **Cambios al alcance del proyecto.** Aplica regla del playbook F4.
- **Actuar fuera de las instrucciones documentadas.** Si una tarea pide algo que no está en el `CLAUDE.md` del proyecto ni en este `CLAUDE_Solemia.md`, **paro y pregunto antes de improvisar**. NO invento patrones nuevos basado en suposiciones.

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

### Convenciones por defecto (sujetas a validación de Andrés/Mario al construir el primer Base Kit)

- **Commits:** Conventional Commits (`feat:`, `fix:`, `docs:`, `refactor:`, `chore:`, `test:`).
- **Branches:** GitHub flow simple (main + feature branches + PR). No git flow — overkill para equipo de 2.
- **Logging:** logs nativos de Vercel + Supabase al inicio. Sentry cuando haya errores recurrentes en producción.
- **Manejo de errores:** try/catch en boundaries (handlers de API, mutations en cliente). React error boundaries para UI. NO try/catch en cada función — genera ruido y oculta bugs.
- **Variables de entorno:** `.env.local` para dev (gitignored), Vercel env vars o equivalente para prod. NUNCA committear `.env`.

---

## Cuando se vaya a crear un proyecto nuevo cliente

### Para Mario (humano que arranca el proyecto)

Checklist paso a paso para arrancar un repo nuevo de proyecto cliente desde cero:

1. **Confirmar que la fase F2 está cerrada.** Propuesta firmada por el cliente y primer pago cobrado. Sin esto, no se arranca código. Detalle del playbook → `03-Ventas/Fases_de_Entrega.html`.
2. **Confirmar la línea de servicio** (L1 / L2 / L3 / combinación) según el diagnóstico de F1.
3. **Crear el repo en GitHub** bajo `cenitvertex/cliente-[slug]` (ej: `cenitvertex/cliente-jonathan-dental`, `cenitvertex/cliente-pizzeria-X`, `cenitvertex/cliente-fletes-padilla`).
4. **Clonar el Base Kit correspondiente** a la línea (cuando exista — hoy todavía está en construcción; mientras tanto, repo vacío).
5. **Crear `CLAUDE.md` del proyecto** copiando `06-Tecnico/CLAUDE_Template_Proyecto.md` (próximo a crear) y rellenar los campos: cliente, contacto, servicios contratados, módulos activados del Base Kit, gotchas conocidos.
6. **Configurar `.env.local`** con las credenciales del cliente (Supabase URL + keys, Twilio si aplica, OpenAI/Anthropic API key, etc.). NUNCA committear este archivo.
7. **Proteger la rama `master`** en GitHub (Settings → Branches → Add branch protection rule → require pull request review antes de merge). Esto evita que se pushee directo y obliga a revisión.
8. **Primer commit** con estado base post-bootstrap.
9. **Avisar a Andrés** que el repo está listo para arrancar F4 (diseño/blueprint).

### Para Claude / Cursor cuando abre un repo nuevo de cliente

Cuando entres por primera vez a un repo `cenitvertex/cliente-[slug]`:

1. **Lee primero el `CLAUDE.md` del proyecto** (raíz del repo). Es la fuente de info específica del cliente.
2. **Lee este `CLAUDE_Solemia.md`** completo si nunca lo has leído. Las reglas cardinales son no-negociables.
3. **Identifica la línea de servicio** y los módulos activados del Base Kit (debe estar en el CLAUDE.md del proyecto).
4. **Confirma que el spec del proyecto** (resultado de F3) está disponible — busca carpeta `/spec/` en el repo o referencia en el CLAUDE.md del proyecto. **Si no está, NO empieces a construir** — pregunta a Mario o Andrés antes.
5. **Revisa el estado del Base Kit clonado** — entiende qué viene por default antes de tocar nada (estructura de carpetas, tablas creadas, módulos activados, dependencias instaladas).
6. **Si falta info crítica** (campos en el spec, decisiones del cliente, credenciales, módulos a activar), pregunta. NO inventes valores plausibles para "destrabar".
7. **Aplica las reglas cardinales anti-noodle** desde el primer commit que hagas. No "primero arranco y después limpio".

---

## Decisiones técnicas: cerradas y por documentar

### Cerradas (en uso, no se discuten sin razón fuerte)

- **Supabase para auth** en proyectos con <50 usuarios internos (no Clerk).
- **TypeScript strict mode** obligatorio.
- **Migrations versionadas** siempre.
- **Base Kit + Módulos** como motor de productización.
- **7 fases del playbook** como flujo estándar de proyecto.
- **`bases-de-solemia`** como single source of truth de docs internos.
- **Naming de repos cliente:** `cliente-[slug]` (ej: `cliente-jonathan-dental`, `cliente-pizzeria-X`, `cliente-fletes-padilla`).
- **Feature branches + PR para todo cambio en repo de cliente** (no push directo a master). Andrés (o Mario, según el caso) revisa antes de merge.

### Por documentar al construir el primer Base Kit L3

Solemia **NO toma estas decisiones por adelantado en abstracto**. Cuando Mario arranque la primera implementación real de Next.js + Supabase (probablemente Consultorio Dental Jonathan García o Pizzería), las herramientas que Claude Code o Cursor configuren por default al inicializar el proyecto son las que quedan estandarizadas. Se documentan acá en ese momento, no antes — esto evita debates teóricos sobre herramientas que aún no se usan.

Categorías a documentar cuando se construya el Base Kit L3:

- **Linter / Formatter** (probable: ESLint + Prettier o Biome — se decide al ver qué Claude Code instala por default).
- **Package manager** (probable: pnpm o npm — se decide al `init`).
- **Testing framework** (cuando se decida hacer tests — probable Vitest).
- **UI library** (probable: shadcn/ui + Tailwind si Claude Code lo sugiere).
- **CI/CD pipeline** (cuando haga falta — probable GitHub Actions + Vercel auto-deploy).
- **Branch strategy formal** (qué tan estricto: hoy `master` protegido + PR es el mínimo; si hace falta más estructura, se decide ahí).

Cuando una de estas se documente, se mueve a "Cerradas" arriba y se registra en `Decision_Log.md` (próximo a crear) la opción elegida y por qué.

---

## Cómo extender este doc en cada proyecto cliente

El `CLAUDE.md` de cada proyecto cliente debe seguir esta plantilla mínima:

```markdown
# CLAUDE.md — [Nombre del Proyecto]

## Hereda
Este proyecto sigue la constitución técnica de Solemia:
https://github.com/cenitvertex/bases-de-solemia/blob/master/06-Tecnico/CLAUDE_Solemia.md

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
| 2026-04-28 | v0.1 | Mario E. (con apoyo de Claude) | Creación inicial. Constitución redactada con base en docs vigentes de `01-Marca`, `02-Servicios`, `03-Ventas`, `04-Operacion`. Pendiente: validación de Andrés sobre roles, reglas cardinales, y decisiones abiertas. |
| 2026-04-28 | v0.2 | Mario E. (con apoyo de Claude) | Aplicado feedback inicial de Mario: (1) añadida sección "Cuando se vaya a crear un proyecto nuevo cliente" con sub-secciones para humano y para IA; (2) ajustada regla "Claude NUNCA hace solo testing" — solo aplica a datos reales de producción, tests con mocks/seeds están OK; (3) añadida regla "Claude NUNCA actúa fuera de instrucciones documentadas"; (4) reemplazada sección "Decisiones abiertas con propuesta tentativa" por "Decisiones por documentar al construir el primer Base Kit L3" — las herramientas se cierran al usarlas, no antes; (5) añadido a "Cerradas": naming de repos `cliente-[slug]` y feature branches + PR obligatorio. |

---
---

## ⚙️ Para la IA — instrucciones de mantenimiento

> **Si eres humano, no necesitas leer esta sección.** Todo lo importante está arriba.

### Triggers para actualizar este doc

- Cambio en valores, propósito, o postura de Solemia (raro, pero sucede). Coordinar con doc de marca.
- Nueva regla cardinal anti-noodle aprobada por Mario y Andrés (aprobación explícita requerida — ver "Reglas de qué NO hacer").
- Cambio en división de roles entre Mario / Andrés / Cliente.
- Nuevo doc creado en `06-Tecnico/` que merece referencia cruzada.
- Decisión técnica "por documentar" que se cierra al usarse en proyecto real (mover de "por documentar" a "cerradas" con nota en `Decision_Log.md`).
- Cambio en cómo Claude debe trabajar en repos de Solemia.
- Cambio en checklist de creación de proyecto nuevo (sección "Cuando se vaya a crear un proyecto nuevo cliente").

### Reglas de qué NO hacer al editar este doc

- **NO añadir reglas anti-noodle "cardinales" sin aprobación explícita** de Mario y Andrés. Las cardinales son estables. Proponer adiciones primero a `Reglas_Anti_Noodle.md`, promoverlas aquí solo cuando se vuelven verdaderamente cardinales y aprobadas.
- **NO modificar identidad / valores / postura** sin coordinación con el doc de marca (`01-Marca/`).
- **NO inventar convenciones técnicas** que no estén validadas. Si una decisión está en "por documentar", **NO la cierres tú** — espera a que se use en proyecto real.
- **NO eliminar referencias cruzadas** aunque el doc referenciado no exista todavía. Marcar "(próximo a crear)".
- **NO simplificar al punto de perder utilidad.** Este doc puede ser largo — su valor está en ser COMPLETO y autoritativo, no en ser corto.

### Cómo validar un cambio antes de aplicarlo

- Cualquier edición a este doc debe ir como PR a `bases-de-solemia` con descripción que diga: qué cambió, por qué, y quién aprobó.
- Cada 6 meses, revisar la sección "Decisiones por documentar" — si llevan más de 6 meses sin cerrarse y los proyectos están activos, escalar a Mario/Andrés en checkpoint dedicado.
- Cada vez que se cierre una decisión por documentar, registrar en `Decision_Log.md` con fecha, opciones consideradas, decisión tomada, y razón.
- Cuando se actualice el checklist "Cuando se vaya a crear un proyecto nuevo cliente", validar que sea consistente con `Bootstrap_Repo.md` (próximo a crear) — ambos deben decir lo mismo.

### Si encuentras conflicto entre este doc y un CLAUDE.md de proyecto

- **Las reglas cardinales de este doc PREVALECEN siempre** sobre cualquier CLAUDE.md de proyecto.
- Las "decisiones por documentar" pueden cerrarse localmente en un proyecto si el caso lo amerita; debe documentarse el por qué en el CLAUDE.md de ese proyecto y proponerse PR a este doc para promover la decisión a "cerrada" oficialmente.
- Convenciones específicas del proyecto sobreescriben las de default solo si están **explícitamente justificadas** en el CLAUDE.md del proyecto (no por omisión).

### Cómo se referencia este doc desde un proyecto cliente

El `CLAUDE.md` de un proyecto cliente debe iniciar con:

```markdown
## Hereda
Este proyecto sigue la constitución técnica de Solemia:
https://github.com/cenitvertex/bases-de-solemia/blob/master/06-Tecnico/CLAUDE_Solemia.md

Léelo completo antes de tocar código en este repo. Las reglas cardinales aplican aquí — lo de abajo es solo lo específico de este cliente.
```
