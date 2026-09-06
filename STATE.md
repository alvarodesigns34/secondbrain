# Estado del proyecto — Second Brain

> Cuadro de mando macro para orientarse rápido. Lo mantiene Claude. **No es la autoridad operativa**: si alguna vez este documento discrepa del campo `Turno` de un issue o PR concreto, manda el issue/PR (ver `AGENTS.md`, regla 9). Se actualiza tras cualquier evento relevante (tarea cerrada, decisión tomada, cambio de fase).

**Última actualización:** 2026-09-06 — por Claude (vía rápida, tras confirmación de Grok y Antigravity)

## Fase actual

**Fase 0 — Cimientos del proyecto y protocolo de colaboración.**

No hay todavía código de aplicación. El objetivo de esta fase es que los tres agentes (Claude, Grok, Antigravity) tengamos un espacio de trabajo compartido, un protocolo claro y una forma de comunicarnos a través de GitHub sin depender de coordinación manual constante por parte de Álvaro. Ver [`docs/01-roadmap/ROADMAP.md`](docs/01-roadmap/ROADMAP.md).

## Tareas activas

- Ninguna. Los issues [#2](https://github.com/alvarodesigns34/secondbrain/issues/2) y [#3](https://github.com/alvarodesigns34/secondbrain/issues/3) están cerrados; Grok y Antigravity confirmaron (checklist de arranque de sesión) que ADR-0002 resuelve sus auditorías y que no hay ningún issue/PR abierto con su turno. No hay tareas de producto abiertas todavía.

## Bloqueado

- Nada bloqueado. Hay una acción manual pendiente de Álvaro (no técnica): cambiar la rama por defecto del repositorio a `main` en `Settings → General → Default branch` — ninguna herramienta de las que usamos tiene acceso a ese ajuste. Ver [`BRANCHING.md`](docs/03-process/BRANCHING.md).

## Recientemente completado

- **2026-09-06** — Cimientos del repositorio: estructura de documentación, protocolo de colaboración entre agentes, plantillas de issue/PR, y mensajes de incorporación para Grok y Antigravity. Ver [ADR-0001](docs/02-architecture/decisions/0001-sistema-de-colaboracion-entre-agentes.md).
- **2026-09-06** — Primera auditoría cruzada: Grok ([#2](https://github.com/alvarodesigns34/secondbrain/issues/2)) y Antigravity ([#3](https://github.com/alvarodesigns34/secondbrain/issues/3), informe en [`docs/05-reviews/2026-09-06-auditoria-cimientos.md`](docs/05-reviews/2026-09-06-auditoria-cimientos.md)) auditaron los cimientos de Fase 0 y coincidieron en varios huecos operativos reales (rama por defecto, mutex `Turno` vs `STATE.md`, identidad compartida, arranque de sesión). Claude las resolvió en [ADR-0002](docs/02-architecture/decisions/0002-correcciones-de-protocolo-tras-primera-auditoria.md).
- **2026-09-06** — Grok y Antigravity aplicaron el nuevo checklist de arranque de sesión y confirmaron, cada uno en su issue, que ADR-0002 resuelve su auditoría sin objeciones nuevas. Grok señaló que esta misma tabla seguía desactualizada tras el cierre de #2/#3 — corregido en esta misma actualización (vía rápida, sin ciclo de revisión completo).

## Decisiones recientes

- [ADR-0001](docs/02-architecture/decisions/0001-sistema-de-colaboracion-entre-agentes.md) — Sistema de colaboración GitHub-nativo entre los tres agentes: Issues como unidad de tarea, PRs como unidad de revisión, campo `Turno` como control de concurrencia, `main` protegida y Claude como único que fusiona.
- [ADR-0002](docs/02-architecture/decisions/0002-correcciones-de-protocolo-tras-primera-auditoria.md) — Correcciones al protocolo tras la primera auditoría cruzada de Grok y Antigravity: jerarquía explícita `Turno` (issue/PR) > `STATE.md`, vía rápida para cambios triviales, firma y trailers de commit obligatorios, checklist de arranque de sesión, watchdog para turnos bloqueados, y aclaraciones de convención (ADR en borrador, nombres en `docs/05-reviews/`).

## Próximo paso propuesto

1. ~~Claude cierra los issues #2 y #3~~ — hecho. ~~Grok y Antigravity aplican el checklist de arranque~~ — hecho, ambos confirmaron que ADR-0002 resuelve su auditoría sin objeciones nuevas.
2. **Acción pendiente de Álvaro** (la única que queda abierta): cambiar la rama por defecto del repositorio a `main` (`Settings → General → Default branch`), y opcionalmente activar "Automatically delete head branches" en `Settings → General`.
3. Cuando Álvaro quiera, empezamos a cerrar las preguntas abiertas de `docs/00-vision/VISION.md` (incluida la de captura rápida / escalabilidad del grafo) para definir el alcance del MVP y pasar a la Fase 1. Hasta entonces, Grok y Antigravity quedan en reposo — no hay tareas ni revisiones que asignarles.
