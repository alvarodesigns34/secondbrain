# 0001 — Sistema de colaboración entre agentes

**Estado:** Aceptada
**Fecha:** 2026-09-06
**Propuesta por:** Claude
**Decidida por:** Claude

## Contexto

Tres agentes de IA (Claude, Grok, Antigravity) y un fundador (Álvaro) trabajan en el mismo proyecto desde conversaciones completamente separadas, sin canal de comunicación directo entre agentes. GitHub es el único espacio compartido real. Álvaro no quiere coordinar manualmente cada detalle técnico: quiere poder decir "seguid" o "mira lo que ha hecho Grok" y que el sistema funcione sin fricción.

Esto plantea varios problemas concretos a resolver:
- Cómo se especifica una tarea de forma que Grok pueda implementarla sin ida y vuelta constante.
- Cómo se revisa el trabajo de forma que Antigravity pueda ser exigente sin bloquear indefinidamente.
- Cómo se evita que dos agentes trabajen sobre lo mismo a la vez, sin coordinación en tiempo real.
- Cómo se conserva el criterio de arquitectura de Claude a través de sesiones y del tiempo, dado que cada sesión de cada agente empieza sin memoria de las anteriores.
- No sabemos si Grok y Antigravity operan bajo identidades de GitHub propias y diferenciadas o bajo la misma cuenta que Álvaro, así que no podemos depender de que GitHub distinga "quién es quién" a nivel de permisos.

## Decisión

Adoptamos un sistema **GitHub-nativo** con las siguientes piezas:

1. **GitHub Issues como unidad de tarea.** Claude especifica cada tarea como un issue con una plantilla fija (contexto, objetivo, alcance, criterios de aceptación, restricciones).
2. **Pull Requests como unidad de implementación y revisión.** Grok implementa en una rama por tarea y abre una PR referenciando el issue; Antigravity revisa ahí, con un veredicto explícito.
3. **Un campo `Turno` explícito en el cuerpo de cada issue/PR** (`Turno: Grok` / `Turno: Antigravity` / `Turno: Claude`) como mecanismo de concurrencia: un agente solo actúa donde tiene el turno. Esto no depende de que existan etiquetas de GitHub previamente configuradas — funciona con texto plano desde el primer día.
4. **`main` como rama siempre estable**, sin push directo; todo entra por PR (GitHub-flow simple, sin `develop` ni ramas de larga vida).
5. **Claude como único agente que fusiona PRs a `main`**, y como responsable último de la coherencia de arquitectura.
6. **ADRs para decisiones no triviales**, en `docs/02-architecture/decisions/`, siempre confirmadas por Claude.
7. **`STATE.md` como panel vivo del "ahora mismo"**, mantenido por Claude, para que cualquiera (agente o Álvaro) sepa en segundos qué está pasando sin reconstruir el historial completo de issues y PRs.

Detalle completo del flujo en [`docs/03-process/WORKFLOW.md`](../../03-process/WORKFLOW.md), [`BRANCHING.md`](../../03-process/BRANCHING.md) y [`TASKS.md`](../../03-process/TASKS.md).

## Alternativas consideradas

- **GitHub Projects (tablero Kanban) como fuente de verdad del estado.** Descartado por ahora: añade una superficie de configuración y de permisos adicional entre tres agentes con acceso desconocido/desigual a la API de GitHub, para un beneficio (vista Kanban) que `STATE.md` + issues ya cubre a este tamaño de equipo. Se puede añadir más adelante sin romper nada de lo decidido aquí.
- **Depender solo de etiquetas (labels) de GitHub para el estado y el turno.** Descartado como mecanismo único: requiere que alguien cree las etiquetas de antemano en el repositorio (no hay herramienta disponible para crearlas automáticamente vía API en este momento) y, si no están creadas, el sistema completo deja de funcionar. Se documentan como mejora opcional en `docs/03-process/TASKS.md`, pero el campo `Turno` en el cuerpo del issue/PR es el mecanismo real y obligatorio.
- **Coordinación en tiempo real entre agentes (canal compartido, webhook entre sesiones).** Descartado: los tres agentes no tienen garantizada esa integración, y el propio Álvaro prefiere no depender de que exista. GitHub como memoria asíncrona es más robusto ante esa incertidumbre.
- **Cualquier agente puede fusionar su propia PR si Antigravity la aprueba.** Descartado: concentra el control de arquitectura en Claude de forma explícita, tal y como pide Álvaro, y evita que una aprobación de Antigravity (revisor, no arquitecto) se confunda con luz verde de producto/arquitectura.

## Consecuencias

- El sistema funciona incluso si nunca se configuran etiquetas de GitHub, porque el campo `Turno` es texto plano dentro del propio issue/PR.
- Si en el futuro Grok y Antigravity tienen cuentas de GitHub propias y diferenciadas, se puede reforzar esto con reglas técnicas reales (branch protection con revisores obligatorios, CODEOWNERS). Mientras tanto, el cumplimiento de "Claude fusiona, Antigravity revisa, Grok implementa" es una **regla de proceso**, no una restricción técnica impuesta por GitHub — depende de que cada agente respete su rol tal y como se describe en `AGENTS.md`.
- Añade disciplina de mantenimiento: si `STATE.md` o el campo `Turno` no se actualizan, el sistema pierde fiabilidad. Es responsabilidad de Claude mantenerlo, y de cualquier agente señalar cuando lo vea desactualizado.
- Deja abierta la puerta a adoptar GitHub Projects u otra herramienta más adelante si el volumen de tareas lo justifica, sin necesidad de deshacer nada de esta decisión.
