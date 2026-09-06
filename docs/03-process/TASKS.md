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

Este bloque es texto plano en el cuerpo del issue/PR — funciona sin ninguna configuración previa del repositorio. Cualquier agente que cambie de turno actualiza este bloque como parte de su entrega (ver [`WORKFLOW.md`](WORKFLOW.md) para quién lo cambia y cuándo).

## Etiquetas de GitHub (opcional, complementario)

Si en algún momento se crean etiquetas en el repositorio, se recomienda esta lista (puramente para poder filtrar en la interfaz de GitHub — nunca son la fuente de verdad, esa es siempre el bloque de metadatos del cuerpo):

`status:proposed`, `status:in-progress`, `status:in-review`, `status:changes-requested`, `status:approved`, `status:done`, `status:blocked`, `type:foundation`, `type:feature`, `type:bug`, `type:research`, `type:architecture`, `type:finding`, `priority:p0`, `priority:p1`, `priority:p2`.

## Hallazgos fuera de una tarea

Si Antigravity (o cualquiera) encuentra un problema que no está ligado a una PR concreta en curso (por ejemplo, durante una auditoría libre), lo documenta como issue con la plantilla [`.github/ISSUE_TEMPLATE/review-finding.md`](../../.github/ISSUE_TEMPLATE/review-finding.md), con `Turno: Claude`, para que Claude lo triage (lo convierte en tarea, lo descarta explicando por qué, o lo apunta como investigación pendiente).

## Cómo sabemos qué está pasando sin recorrer todo GitHub

- **Qué está en progreso ahora mismo:** [`STATE.md`](../../STATE.md), sección "En progreso" y "Turno de cada agente ahora mismo".
- **Qué está terminado:** issue cerrado + PR fusionada + entrada en la sección "Recientemente completado" de `STATE.md` (y, si implicó una decisión, su ADR en `docs/02-architecture/decisions/`).
- **Qué se decidió y por qué:** `docs/02-architecture/decisions/`, indexado en su [`README.md`](../02-architecture/decisions/README.md).

`STATE.md` es un resumen, no un sustituto de GitHub: para el detalle completo de una tarea siempre se puede (y se debe, si hay duda) ir al issue/PR original.
