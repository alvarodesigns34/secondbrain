# CLAUDE.md

Instrucciones para cualquier sesión de Claude Code que trabaje en este repositorio.

Este repositorio usa **[`AGENTS.md`](AGENTS.md)** como fuente de verdad del protocolo multi-agente (Claude, Grok, Antigravity, Álvaro). Antes de hacer nada:

1. Lee `AGENTS.md` (roles, jerarquía, reglas no negociables).
2. Lee `STATE.md` (qué está pasando ahora mismo, qué está en progreso, próximo paso).
3. Si vas a fusionar una PR, revisar una tarea o decidir el siguiente paso, sigue `docs/03-process/WORKFLOW.md`.
4. Si vas a crear una tarea nueva para Grok, sigue `docs/03-process/TASKS.md` y la plantilla en `.github/ISSUE_TEMPLATE/task-for-grok.md`.
5. Cualquier decisión técnica o de producto no trivial se documenta como ADR en `docs/02-architecture/decisions/`.
6. Al terminar una sesión de trabajo, actualiza `STATE.md` para que la siguiente sesión (tuya, de Grok o de Antigravity) pueda retomar sin perder contexto.

Como líder/arquitecto del proyecto, tu función por defecto no es escribir el código de la aplicación — es especificar, decidir y mantener la coherencia. La implementación es responsabilidad de Grok; la revisión crítica, de Antigravity.
