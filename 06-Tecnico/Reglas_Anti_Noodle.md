# Reglas Anti-Noodle de Solemia

> **Estado:** Draft v0.1 (rewrite con investigación de Andrés). Última edición: 2026-04-28.

---

## Para personas

"Noodle code" es código que funciona el primer día y nadie puede tocar el día 90 sin romper algo. Es el riesgo número uno de construir con IA sin disciplina. Este documento contiene **22 reglas** que en conjunto previenen el noodle. Las **10 primeras son cardinales** — están en `AGENTS.md` (root) porque la IA las debe leer antes de tocar código en cualquier repo de Solemia. Las **12 restantes son refinamientos** que aplican cuando la situación específica las invoca.

Cada regla tiene **el qué** (la regla en sí), **el por qué** (con cita a la fuente o incidente que la motivó), y **el cómo aplicarla** (qué hacer en práctica). Si una regla resulta inaplicable o contraproducente en un proyecto específico, NO se rompe en silencio — se documenta la excepción en el `CLAUDE.md` del proyecto y se propone un PR a este doc para discutir si la regla cambia.

Las fuentes principales son: la investigación interna de Solemia (abril 2026 con 2,000+ fuentes), el manual *Vibecoding profesional 2026*, y los principios de Karpathy, Anthropic, Hashimoto, Ronacher y Willison.

---

## Las 10 reglas cardinales (en AGENTS.md)

### 1. Don't Assume — pregunta antes de inventar

**Por qué:** Karpathy (autor del CLAUDE.md viral con 40k stars) lo lista como regla #1. Anthropic confirma: el modo de falla más común de Claude Code es alucinar APIs, paths o convenciones que "deberían existir" pero no existen. En Solemia es regla operativa explícita pedida por Mario y Andrés.

**Cómo aplicarla:** si una decisión, convención, dato o credencial no está documentada, pregunta. Vale más una pregunta extra que un atajo basado en suposición. Aplica a Claude tanto como a humanos.

### 2. Surgical Changes — cambios mínimos, sin sobre-ingeniería

**Por qué:** Karpathy regla #2. La sobre-ingeniería ("ya que estoy lo refactorizo todo") es el origen más frecuente de noodles imposibles de revertir. Anthropic documenta: cuando le pides a Claude "hazlo todo", produce más código pero más frágil que cuando le pides un chunk atómico.

**Cómo aplicarla:** ¿es un fix? cambia solo lo que rompe. ¿es feature nueva? hazla en chunks ≤300 líneas por commit. NO refactor "de paso". Si ves algo malo fuera del scope, propón un commit aparte.

### 3. Preserve What You Don't Understand

**Por qué:** Karpathy regla #3. Borrar código que parece "muerto" pero que era load-bearing es causa típica de regresiones invisibles. Si no entiendes por qué una línea existe, asume que tiene una razón hasta probar lo contrario.

**Cómo aplicarla:** antes de borrar, busca con `git log -- <file>` o `git blame` el contexto. Si sigue sin quedar claro, pregunta o conserva. Mejor un comentario "TODO: investigar por qué este check existe" que un delete optimista.

### 4. Verify Your Work — corre `pnpm check` antes de declarar "listo"

**Por qué:** Karpathy regla #4 + lección de Reddit más citada (2025): la IA escribe mensajes triunfantes sobre código que ni siquiera compila. Anthropic llama a verification loops "the single highest-leverage thing".

**Cómo aplicarla:** después de cada chunk, instrucción obligatoria: `Run pnpm check. Show me the output.` Si declara "listo" sin haber corrido y mostrado output, no le creas. Verify = typecheck + lint + test críticos + build.

### 5. Single source of truth → `bases-de-solemia`

**Por qué:** convenciones, schemas, decisiones y plantillas duplicadas en Notion/Slack/cabezas se desincronizan en semanas. Un solo lugar fuente o no hay fuente.

**Cómo aplicarla:** si una decisión se toma en una llamada, alguien la documenta en `bases-de-solemia` antes de cerrar el día. Si no llegó al repo, no existe.

### 6. Base Kit primero, custom después

**Por qué:** Solemia productiza por componentes. Reinventar lo que ya existe duplica trabajo, fragmenta patrones, y mata el efecto compuesto del Base Kit.

**Cómo aplicarla:** antes de construir cualquier cosa, revisa Base Kit + módulos en `04-Operacion/Aceleracion_Base_Kit_y_Modulos.docx`. Solo construye custom si no existe Y vale la pena. Regla de promoción a módulo: 2-3 usos en producción primero.

### 7. Migrations versionadas SIEMPRE

**Por qué:** tocar producción a mano genera estado divergente entre dev/staging/prod que es imposible de reproducir. Bug de fin de semana = horas de detective work.

**Cómo aplicarla:** todo cambio en DB pasa por Supabase migration versionada. Si no se puede hacer migration (por la razón que sea), no se hace el cambio. Cero excepciones.

### 8. HITL por defecto en clientes nuevos

**Por qué:** mensajes públicos, dinero o decisiones legales emitidas por IA sin supervisión son la causa más común de incidentes con clientes en agencias 2025-2026. Los dueños de PYME se asustan con "autónomo" cuando se trata de su dinero o reputación.

**Cómo aplicarla:** cualquier sistema que toque dinero, comunicación pública, o decisiones contractuales arranca con human-in-the-loop. Quitar HITL solo cuando el cliente lo pida explícitamente y haya 2+ meses de operación sin incidentes.

### 9. No `service_role` en frontend ni MCP write expuesto a usuarios

**Por qué:** la "lethal trifecta" (Simon Willison 2025): LLM con acceso a datos privados + entrada no confiable + capacidad de exfiltrar = catástrofe. CVE-2025-48757 afectó 170+ apps Lovable por RLS faltante. El incidente Supabase MCP + Cursor del 2025 está documentado como caso de estudio.

**Cómo aplicarla:** `service_role` solo en server code (n8n, Edge Functions, Server Actions), nunca expuesto al cliente ni al frontend. MCPs en modo `read_only=true` por default; activar write SOLO en sesiones explícitamente trabajando en una migration. RLS habilitada en TODA tabla expuesta vía PostgREST, aunque solo accedas desde server.

### 10. Aprobación escrita antes de cambiar scope

**Por qué:** scope creep silencioso es el origen #1 de proyectos que se vuelven inviables. La aprobación escrita protege al equipo y al cliente.

**Cómo aplicarla:** si Mario detecta que el alcance creció (técnicamente o en lo conceptual), para y consulta. Andrés decide si va al cliente como cotización adicional. Nada de "ah ya que estoy lo arreglo".

---

## Las 12 reglas complementarias

### 11. Spec antes de código

**Por qué:** Spec-Driven Development (SDD) es el consenso 2025-2026. Sin spec, la IA infiere intenciones y muchas veces falla. Con spec versionada en git, el contexto sobrevive sesiones de Claude y rotación de gente. GitHub Spec Kit, Kiro de AWS y Tessl convergieron en este patrón.

**Cómo aplicarla:** todo feature arranca con `specs/00X-feature/spec.md` en formato EARS (Event-Action-Response-Specification): `WHEN <trigger> the <system> SHALL <behavior>`. Detalle en `06-Tecnico/SDD_Workflow.md`.

### 12. Plan Mode obligatorio en Claude Code

**Por qué:** Anthropic identifica el plan como la fase de mayor leverage por dólar de tokens. Saltarlo es la decisión más cara que existe.

**Cómo aplicarla:** Shift+Tab 2x en Claude Code antes de tocar código. Output del plan a `specs/<feature>/plan.md`. Edita con Ctrl+G antes de aceptar. Solo después se ejecuta.

### 13. Chunks atómicos ≤300 líneas por commit

**Por qué:** Anthropic explícitamente recomienda no pedir features completos de una. Mensajes de Reddit confirman que cuando supera ~300 líneas el output empieza a tener bugs sutiles invisibles.

**Cómo aplicarla:** el patrón canónico por chunk: data layer (schema + migration + service CRUD) → commit; API/routes → commit; UI/componentes → commit; tests E2E → commit.

### 14. AGENTS.md / CLAUDE.md cortos (≤80 líneas)

**Por qué:** HumanLayer reporta que su CLAUDE.md root corporativo es <60 líneas y que **archivos largos hacen que Claude IGNORE las reglas**. Si los archivos de contexto pasan de 80 líneas, Claude empieza a saltearse secciones silenciosamente.

**Cómo aplicarla:** mantener AGENTS.md y CLAUDE.md root ≤80 líneas (idealmente 60). Lo extenso vive en docs especializados que se referencian. Cada vez que algún archivo de contexto supere 80 líneas, refactorizarlo.

### 15. `/clear` agresivo entre features

**Por qué:** Geoffrey Huntley llama a la auto-compactación de contexto "the devil". Una sesión de Claude que arrastra contexto de feature pasada genera decisiones contaminadas.

**Cómo aplicarla:** `/clear` después de cada feature/PR cerrado. Empieza la siguiente sesión limpia con `PROMPT.md` enfocado. Si ya se autocompactó una sesión, considera empezar fresh.

### 16. TypeScript estricto + linter en CI

**Por qué:** los errores que `tsc --noEmit` strict + `eslint --max-warnings 0` detectan son una fracción enorme de los bugs que la IA introduce. Coverage % es teatro; lint+typecheck no.

**Cómo aplicarla:** `tsconfig.json` con `strict: true`, `noUncheckedIndexedAccess: true`, `noImplicitOverride: true`. Linter (Biome o ESLint) en CI con `--max-warnings 0`. Si falla, no mergea. Cero excepciones.

### 17. Harness Engineering — cuando la IA falla 2x, escribir un script

**Por qué:** Mitchell Hashimoto: cuando la IA comete el mismo error 2 veces, escribir un test/lint/script que lo detecte y agregarlo a CLAUDE.md. Es la única manera de que el aprendizaje compuesto.

**Cómo aplicarla:** lleva un mental log de errores recurrentes. Cuando aparezca el mismo 2 veces: añade regla a CLAUDE.md o lint rule local que lo bloquee.

### 18. Pre-commit hooks no negociables

**Por qué:** los guardrails que dependen de la disciplina humana fallan. Los que dependen de hooks que no se pueden saltar funcionan.

**Cómo aplicarla:** Husky + lint-staged corren `biome check --apply` y `tsc --noEmit` en archivos staged. Pre-push corre `pnpm test` y `pnpm build` completos. GitHub Actions CI con jobs paralelos (lint, typecheck, test, build, e2e). Branch protection en `main` SIN bypass — ni siquiera para admins.

### 19. Tests críticos sí, coverage % no

**Por qué:** perseguir 80% coverage es teatro. Perseguir cobertura de los 5 flujos que si rompen el cliente cancela (auth, pagos, agendamiento) es lo que importa.

**Cómo aplicarla:** unit tests en lógica de negocio compleja (cálculos, validaciones, webhooks Stripe). 20-30 Playwright E2E en flujos críticos. NO TDD para UI exploratoria ni prototipos.

### 20. Patrón neuro-simbólico — el LLM no calcula

**Por qué:** los LLMs alucinan números. Cualquier sistema agéntico que toque dinero, inventario o datos fiscales necesita que la IA decida y el código ejecute lo numérico.

**Cómo aplicarla:** prohibición explícita en system prompt: *"You MUST NOT compute totals, counts, averages, or aggregations yourself. Call the appropriate Postgres tool."* Tools con queries SQL parametrizadas (no generación dinámica). Detalle en `06-Tecnico/Architecture_L2.md`.

### 21. Idempotencia obligatoria en webhooks

**Por qué:** webhooks duplicados son la realidad (WhatsApp, Stripe, n8n). Sin idempotencia: cargos duplicados, notificaciones duplicadas, respuestas duplicadas al cliente.

**Cómo aplicarla:** tabla con UNIQUE en `event_id` o `message_id`. `INSERT ... ON CONFLICT DO NOTHING RETURNING *`. Si no devuelve fila, IF node corta el flujo. Verificar HMAC con `crypto.timingSafeEqual`.

### 22. Refactor calendarizado

**Por qué:** sin tiempo dedicado al mantenimiento, la deuda técnica se acumula hasta el punto de no retorno. DailyAiMind/Tabnine 2026 recomienda 20% del tiempo en debt reduction.

**Cómo aplicarla:** cada 4-6 semanas, día de mantenimiento. NO se negocia. Quincenalmente, review de deuda con triggers explícitos: feature que tomaba 3h ahora toma 8h, Claude se "pierde" en archivos largos (>500 líneas), CodeRabbit comenta repetidamente sobre el mismo patrón malo, CI lento (>10 min).

---

## Cómo se actualiza este documento

- Nueva regla cardinal (1-10): requiere aprobación explícita de Mario y Andrés. Se promueve desde "complementaria" cuando se vuelve verdaderamente cardinal.
- Nueva regla complementaria (11+): proponer vía PR con caso documentado.
- Modificar regla existente: PR con razón clara y referencia al incidente o fuente que la motiva.
- Eliminar regla: solo si se demuestra que es contraproducente en >2 proyectos. Documentar en `Decision_Log.md`.

---

## Bitácora de revisiones

| Fecha | Versión | Quién | Notas |
|---|---|---|---|
| 2026-04-28 | v0.1 | Mario E. (con apoyo de Claude) | Creación inicial integrando 11 reglas anti-spaghetti de la investigación de Andrés (Karpathy, Anthropic, Hashimoto, Ronacher, Willison) + las 7 cardinales originales de Solemia + 4 complementarias específicas (idempotencia, neuro-simbólico, refactor calendarizado, harness engineering). |

---
---

## ⚙️ Para la IA — instrucciones de mantenimiento

> Si eres humano, no necesitas leer esta sección.

**Triggers para actualizar:**
- Nueva regla aprobada explícitamente por Mario y Andrés.
- Incidente documentado que invalida una regla existente.
- Promoción de "complementaria" a "cardinal" requiere actualizar también `AGENTS.md`.

**Qué NO hacer al editar:**
- NO añadir reglas sin justificación (cita o incidente concreto).
- NO eliminar reglas históricas — marcarlas como "deprecadas" con razón.
- NO promover regla a "cardinal" sin aprobación explícita Mario+Andrés.
- NO duplicar contenido entre AGENTS.md y este doc — AGENTS.md tiene lista corta, este tiene la lista con justificaciones.

**Sincronización con AGENTS.md:**
- Las reglas 1-10 deben estar reflejadas (en versión corta) en `AGENTS.md`. Si cambia el orden o el wording de una cardinal acá, también cambia en AGENTS.md.
- Las reglas 11+ NO van en AGENTS.md — solo aparecen referenciadas vía link a este doc.

**Si encuentras conflicto entre este doc y un CLAUDE.md de proyecto:**
- Las cardinales (1-10) PREVALECEN sobre cualquier convención local.
- Las complementarias (11-22) pueden tener excepciones documentadas en CLAUDE.md del proyecto si el caso lo amerita.
