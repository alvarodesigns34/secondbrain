# Cómo se crean y gestionan las tareas

## Unidad de tarea: GitHub Issue

Toda tarea de implementación es un **GitHub Issue** en `secondbrain`, creado por Claude a partir de la plantilla [`.github/ISSUE_TEMPLATE/task-for-grok.md`](../../.github/ISSUE_TEMPLATE/task-for-grok.md). Una tarea bien formada incluye:

- **Contexto** — por qué hace falta esto ahora.
- **Objetivo** — qué debe conseguirse, en una frase.
- **Alcance** — qué entra explícitamente y qué queda explícitamente fuera (evita que Grok tenga que adivinar los límites).
- **Restricciones** — qué documentos de arquitectura/ADRs aplican, qué no se puede romper.
- **Criterios de aceptación** — checklist verificable.
- **Definición de hecho** — qué tiene que ser verdad para considerar la tarea terminada (normalmente: PR abierta, plantilla completa, turno cambiado).

Una tarea que no se pueda especificar así (porque falta contexto de producto o de arquitectura) no está lista para convertirse en issue — antes hace falta resolver esa ambigüedad.

## El campo `Turno` — mecanismo obligatorio de concurrencia

Cada issue y cada PR lleva, cerca del principio del cuerpo, un pequeño bloque de metadatos:

```
**Estado:** propuesta / en progreso / en revisión / cambios solicitados / aprobado / hecho / bloqueado
**Turno:** Grok / Antigravity / Claude / Álvaro
**Prioridad:** P0 / P1 / P2
**Tipo:** foundation / feature / bug / research / architecture
```

**Regla de oro: un agente solo actúa sobre un issue o PR si el campo `Turno` lo señala a él.** Esto es lo que nos permite trabajar sin coordinación en tiempo real y sin pisarnos: no hace falta preguntar "¿está esto libre?", basta con mirar el campo.

**Cuál manda si issue y PR discrepan:** mientras una tarea no tiene PR, el `Turno` autoritativo es el del issue. En cuanto se abre una PR para esa tarea, **la PR pasa a ser la autoridad** y el issue deja de ser la referencia operativa (se puede dejar desactualizado sin que eso bloquee nada — quien lo note lo actualiza para reflejar la PR, pero no hace falta editar ambos en cada cambio de turno). Esto evita tener que mantener dos bloques de metadatos sincronizados a mano.

Este bloque es texto plano en el cuerpo del issue/PR — funciona sin ninguna configuración previa del repositorio. Cualquier agente que cambie de turno actualiza este bloque como parte de su entrega (ver [`WORKFLOW.md`](WORKFLOW.md) para quién lo cambia y cuándo).

## Checklist de arranque de sesión (todos los agentes)

El campo `Turno` solo funciona como mutex si cada agente lo comprueba por sí mismo al empezar a trabajar, en vez de depender de que Álvaro avise. Al iniciar cualquier sesión de trabajo en este repositorio:

1. Lee [`STATE.md`](../../STATE.md) para tener contexto de conjunto.
2. Lista los issues abiertos cuyo cuerpo contenga `Turno: <tú>`.
3. Lista las PRs abiertas cuyo cuerpo contenga `Turno: <tú>`.
4. Actúa solo sobre esos. Si no hay ninguno, no inventes trabajo: dilo brevemente (un comentario, o simplemente no actuar) y termina la sesión.

Si un turno lleva bloqueado sin avance de forma injustificada, Claude o Álvaro pueden reclamarlo (`Turno: Claude`) para desatascar el flujo — ver `AGENTS.md`, regla 11.

## Vía rápida (Fast-Track) para cambios triviales

El ciclo completo de cuatro pasos (Claude → Grok → Antigravity → Claude) es el camino por defecto, pero es un coste innecesario para cambios que no tienen riesgo real de romper nada ni de tocar una decisión de arquitectura. Califican para vía rápida:

- Corrección de erratas, enlaces rotos o formato cosmético en documentación existente.
- Archivos **nuevos e independientes** dentro de `docs/04-research/` o `docs/05-reviews/` (una investigación o una auditoría no modifica, por sí sola, ningún documento de protocolo o arquitectura).

En estos casos, **sigue sin haber push directo a `main`** (Regla 1 de `AGENTS.md` no tiene excepciones), pero Claude puede abrir la PR correspondiente y fusionarla directamente, sin esperar el paso de Antigravity. Cualquier cambio que toque `AGENTS.md`, `ARCHITECTURE.md`, una ADR, o el propio `docs/03-process/`, **no** es vía rápida — sigue el ciclo completo aunque parezca pequeño, porque ahí es donde un error silencioso cuesta más caro.

## Etiquetas de GitHub (opcional, complementario)

Si se crean etiquetas en el repositorio, se recomienda esta lista (puramente para poder filtrar en la interfaz de GitHub — nunca son la fuente de verdad, esa es siempre el bloque de metadatos del cuerpo). La herramienta para crearlas vía API ya existe si se quiere adoptar esto en el futuro; seguimos sin depender de ellas por diseño (ver [ADR-0001](../02-architecture/decisions/0001-sistema-de-colaboracion-entre-agentes.md)), no por limitación técnica:

`status:proposed`, `status:in-progress`, `status:in-review`, `status:changes-requested`, `status:approved`, `status:done`, `status:blocked`, `type:foundation`, `type:feature`, `type:bug`, `type:research`, `type:architecture`, `type:finding`, `priority:p0`, `priority:p1`, `priority:p2`.

## Hallazgos fuera de una tarea

Si Antigravity (o cualquiera) encuentra un problema que no está ligado a una PR concreta en curso (por ejemplo, durante una auditoría libre), lo documenta como issue con la plantilla [`.github/ISSUE_TEMPLATE/review-finding.md`](../../.github/ISSUE_TEMPLATE/review-finding.md), con `Turno: Claude`, para que Claude lo triage (lo convierte en tarea, lo descarta explicando por qué, o lo apunta como investigación pendiente). Un hallazgo de tipo `foundation` que no encaja bien en esa plantilla (por ejemplo, una auditoría estructural como la de `#2`) puede abrirse como issue libre, siempre con el bloque de metadatos canónico de arriba — no hace falta una plantilla dedicada para cada tipo de issue.

## Decisiones de arquitectura descubiertas durante una tarea (ADR en borrador)

Si mientras se implementa una tarea aparece una decisión de arquitectura no trivial, la PR añade el archivo directamente en `docs/02-architecture/decisions/` pero **sin numerar**: `DRAFT-<slug-corto>.md`, con `Estado: Propuesta`. Quien no propone la ADR no le asigna número — así dos PRs abiertas a la vez nunca compiten por el mismo `NNNN`. Al fusionar, Claude le asigna el siguiente número secuencial (renombra el archivo), actualiza `docs/02-architecture/decisions/README.md` y confirma su `Estado`.

## Cómo sabemos qué está pasando sin recorrer todo GitHub

- **Qué está en progreso ahora mismo:** [`STATE.md`](../../STATE.md), tabla de tareas activas.
- **Qué está terminado:** issue cerrado + PR fusionada + entrada en la sección "Recientemente completado" de `STATE.md` (y, si implicó una decisión, su ADR en `docs/02-architecture/decisions/`).
- **Qué se decidió y por qué:** `docs/02-architecture/decisions/`, indexado en su [`README.md`](../02-architecture/decisions/README.md).

`STATE.md` es un resumen, no un sustituto de GitHub: para el detalle completo de una tarea siempre se puede (y se debe, si hay duda) ir al issue/PR original. Si alguna vez `STATE.md` contradice el `Turno` real de un issue/PR, manda el issue/PR (ver `AGENTS.md`, regla 9).
