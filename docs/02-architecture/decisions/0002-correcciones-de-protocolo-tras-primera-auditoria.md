# 0002 — Correcciones de protocolo tras la primera auditoría cruzada

**Estado:** Aceptada
**Fecha:** 2026-09-06
**Propuesta por:** Grok ([issue #2](https://github.com/alvarodesigns34/secondbrain/issues/2)) y Antigravity ([issue #3](https://github.com/alvarodesigns34/secondbrain/issues/3))
**Decidida por:** Claude

## Contexto

Tal como pedían sus mensajes de incorporación, la primera acción de Grok y Antigravity fue auditar los cimientos de Fase 0 ([ADR-0001](0001-sistema-de-colaboracion-entre-agentes.md)) antes de que se abriera ninguna tarea de producto. Ambas auditorías, hechas de forma independiente, convergieron en los mismos huecos operativos reales — no cosméticos — y uno de ellos se manifestó en la práctica durante la propia auditoría: Antigravity publicó su informe con dos push directos a `main` y a la rama de sesión de Claude Code, exactamente la "paradoja" que su propio hallazgo señalaba (Regla 1 de `AGENTS.md` prohíbe el push directo, pero el protocolo no daba una vía distinta para publicar una auditoría).

Los puntos en los que ambas auditorías coincidieron o se complementaron:

1. La rama por defecto del repositorio no es `main` (sigue siendo `claude/secondbrain-architecture-6yur02`, fijada por GitHub antes de que existiera ningún commit) — riesgo de que una rama nueva nazca del sitio equivocado.
2. Doble fuente de verdad entre el campo `Turno` (issue/PR) y `STATE.md`, sin regla de desempate si discrepan, y una tabla de `STATE.md` que no soportaba varias tareas en paralelo.
3. Identidad de GitHub compartida entre los tres agentes, sin convención de firma en comentarios ni de trailer en commits — imposible saber quién hizo qué.
4. El mecanismo `Turno` solo funciona si alguien avisa de que hay trabajo — no había checklist de arranque de sesión ni forma de desatascar un turno bloqueado sin la intervención de Álvaro.
5. (Solo Grok) Ambigüedad sobre dónde vive un ADR en borrador durante una PR de feature, y una convención de nombre inconsistente para `docs/05-reviews/` entre distintos documentos.
6. (Solo Antigravity) Falta de una "vía rápida" para cambios triviales, ausencia de restricciones de entorno anfitrión en `ARCHITECTURE.md`, y un riesgo de producto real (fricción de captura rápida y degradación visual del grafo a escala) que merece quedar como pregunta abierta de visión, no como hallazgo de proceso.

Ambos coincidieron en que el diseño conceptual y la jerarquía de `AGENTS.md` son sólidos — lo que fallaba era el contrato operativo contra el repositorio real, no el diseño en sí.

## Decisión

Se adoptan las siguientes correcciones, aplicadas directamente en esta misma PR:

1. **Jerarquía explícita de fuentes de verdad** (`AGENTS.md` regla 9, `TASKS.md`): el `Turno` del issue manda mientras no haya PR; en cuanto se abre una PR para la tarea, **la PR es la autoridad** y el issue deja de ser la referencia operativa. `STATE.md` pasa a ser explícitamente un cuadro de mando macro, nunca la autoridad de una tarea individual — ante discrepancia, gana siempre el issue/PR.
2. **`STATE.md` con tabla multi-tarea**, tal como propuso Antigravity (`Tarea / Responsable / Turno / Estado`), en vez de una fila fija por agente que no escala a trabajo en paralelo.
3. **Firma y trailers de commit obligatorios** (`AGENTS.md` regla 10, `BRANCHING.md`): toda firma de texto (`— Grok` / `— Claude` / `— Antigravity` / `— Álvaro`) al final de comentarios/issues/PRs, y trailers de commit (`Implemented-By:`, `Reviewed-By:`, `Documented-By:`, `Decided-By:`) para dejar rastro de autoría con una identidad de GitHub compartida.
4. **Checklist de arranque de sesión** para los tres agentes (`TASKS.md`, `WORKFLOW.md`): leer `STATE.md`, listar issues/PRs propias por `Turno`, actuar solo sobre esas, no inventar trabajo si no hay ninguna.
5. **Watchdog de turno bloqueado** (`AGENTS.md` regla 11): Claude o Álvaro pueden reclamar un turno que lleve bloqueado sin avance injustificado.
6. **Vía rápida (fast-track)** para typos, enlaces rotos, formato cosmético, y archivos nuevos e independientes en `docs/04-research/`/`docs/05-reviews/`: siguen sin poder saltarse el requisito de PR (Regla 1 no tiene excepciones), pero Claude puede fusionarlos sin pasar por el ciclo completo de revisión de Antigravity. Cualquier cambio a `AGENTS.md`, `ARCHITECTURE.md`, una ADR o `docs/03-process/` queda explícitamente **fuera** de la vía rápida.
7. **Convención de ADR en borrador**: quien descubre una decisión de arquitectura durante una tarea añade el archivo sin numerar (`DRAFT-<slug>.md`, `Estado: Propuesta`) en su propia PR; Claude asigna el número secuencial real al fusionar, evitando colisiones entre PRs paralelas.
8. **Convención única para `docs/05-reviews/`**: `AAAA-MM-DD-tema.md` (como ya usaban `WORKFLOW.md` y `docs/05-reviews/README.md`), reservando `NNNN` solo para ADRs. Se renombra el informe ya publicado de Antigravity a `2026-09-06-auditoria-cimientos.md` y se corrige la referencia que quedó desalineada en `STATE.md` y en `docs/agent-briefs/ANTIGRAVITY.md`.
9. **Restricción de entorno en `ARCHITECTURE.md`**: se documenta que el entorno de Álvaro es Windows con PowerShell, priorizando herramientas multiplataforma.
10. **Riesgo de producto de Antigravity** (fricción de captura rápida / escalabilidad del grafo) se incorpora como ampliación de una pregunta abierta existente en `VISION.md` y como nota explícita — no bloqueante — en `ARCHITECTURE.md`, dejando la decisión a Álvaro en Fase 1, no resuelta aquí.
11. **Plantilla `review-finding.md`** alineada al bloque de metadatos canónico (`Estado`/`Turno`/`Prioridad`/`Tipo`).

Lo que **no** se cambia: la Regla 1 (`main` protegida, cero push directo, sin excepciones) se mantiene intacta — el punto 6 resuelve la paradoja señalada por Antigravity acortando el ciclo de revisión, no debilitando la regla de fusión. Tampoco se crean etiquetas de GitHub ni cuentas de agente separadas — ambas cosas siguen aparcadas por las mismas razones de [ADR-0001](0001-sistema-de-colaboracion-entre-agentes.md).

## Alternativas consideradas

- **Autorizar push directo a `main` para `docs/04-research/`/`docs/05-reviews/`** (propuesta explícita de Antigravity, Opción B de su informe). Descartada: es exactamente la brecha que el Riesgo 3 de la propia auditoría de Antigravity señala (sin identidades separadas ni branch protection, una excepción a "cero push directo" es un precedente peligroso, no una simplificación segura). La vía rápida con PR ligera consigue el mismo alivio de fricción sin abrir esa brecha.
- **Mantener `Turno` duplicado y sincronizado a mano en issue y PR** (una de las dos opciones que planteaba Grok). Descartada frente a "la PR manda en cuanto existe": es más simple, no depende de que nadie recuerde actualizar dos sitios, y sigue dejando claro dónde mirar en cada momento.
- **Resolver el hallazgo de "grafo como interfaz única" con una decisión técnica ahora mismo** (por ejemplo, mandatar una capa de inbox). Descartada: es una decisión de producto, no de proceso — le corresponde a Álvaro en Fase 1, con la información de Antigravity ya incorporada como pregunta abierta cualificada en `VISION.md`.
- **No hacer nada y esperar a que el problema de rama por defecto se manifieste como fallo real.** Descartado: ambas auditorías lo identificaron de forma independiente como bloqueante potencial de la primera tarea real; corregirlo ahora (documentando la mitigación, ya que el cambio de configuración en sí requiere una acción manual de Álvaro) cuesta mucho menos que depurarlo después de que ya haya causado una PR con contenido incorrecto.

## Consecuencias

- El protocolo gana un contrato operativo mucho más preciso sin cambiar su diseño de fondo — confirma que la arquitectura de ADR-0001 era correcta y que lo que faltaba era especificar el "cómo" en el borde con el repositorio real.
- Queda una acción manual pendiente de Álvaro que ninguna herramienta de las que usamos puede hacer por él: cambiar la rama por defecto del repositorio a `main` (`Settings → General → Default branch`). Se documenta en `STATE.md`, `BRANCHING.md` y se traslada explícitamente al fundador.
- La vía rápida introduce un criterio que hay que aplicar con criterio propio (qué cuenta como "cosmético") — si en la práctica se abusa de ella para colar cambios de fondo, es una señal de que hay que endurecer la definición, no de que la vía rápida esté mal diseñada.
- El checklist de arranque de sesión y el watchdog reducen la dependencia de Álvaro como "despertador", que era justo el objetivo original de ADR-0001 y que la práctica (esta misma ronda, donde Álvaro tuvo que trasladar el resultado de ambas auditorías) mostró que todavía no se cumplía del todo.
