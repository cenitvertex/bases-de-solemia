# CLAUDE.md

@AGENTS.md

## Específico de Claude Code

- **Plan Mode obligatorio** antes de modificar código (Shift+Tab 2x en Claude Code). El output del plan se guarda en `specs/<feature>/plan.md`.
- **`/clear` agresivo** entre features distintas — la degradación de contexto es el modo de falla #1 según Anthropic.
- **`/compact`** cuando llegues a 70% del context window, especificando qué preservar.
- Si `CLAUDE.md + AGENTS.md + reglas + MCPs activos` ya consumen >40k tokens, considera abrir nueva sesión limpia con un `PROMPT.md` enfocado.
- Subagents (`.claude/agents/`), slash commands custom (`.claude/commands/`), skills (`.claude/skills/`) y hooks (`.claude/settings.json`) son las primitivas oficiales — usarlas cuando aplique. Detalle en `06-Tecnico/Convenciones_Codigo.md` sección "Claude Code".
- MCPs esenciales: Context7, Supabase MCP (read-only por default, write solo en migrations), GitHub MCP, Playwright MCP, Sentry MCP. Configurar en `~/.claude.json`.
