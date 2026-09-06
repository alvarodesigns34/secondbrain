# Ramas y Pull Requests

Estrategia: **GitHub-flow simple** — una rama estable (`main`) y ramas de trabajo de corta duración, una por tarea. Sin `develop`, sin ramas de larga vida: con tres agentes y un producto todavía sin construir, una jerarquía de ramas más compleja añade coste de coordinación sin beneficio real.

## `main`

- Siempre debe quedar en un estado coherente (documentación consistente ahora; también compilable/desplegable cuando exista código de aplicación).
- **Nadie hace push directo.** Todo cambio entra por Pull Request.
- **Solo Claude fusiona PRs a `main`** (ver [`WORKFLOW.md`](WORKFLOW.md) y [ADR-0001](../02-architecture/decisions/0001-sistema-de-colaboracion-entre-agentes.md)).

**Nota de exigibilidad real:** GitHub solo puede exigir esto de forma técnica (branch protection, revisores obligatorios) si cada agente opera con una identidad de GitHub propia y diferenciada. Si Grok, Antigravity y Álvaro comparten la misma cuenta/token, esta regla es de **proceso**, no una restricción impuesta por GitHub — cada agente la respeta porque así lo define `AGENTS.md`, no porque GitHub se lo impida técnicamente. Se recomienda a Álvaro activar de todos modos, ya mismo, en `Settings → Branches → Branch protection rules` sobre `main`: exigir Pull Request antes de fusionar y prohibir force-push (exigir aprobación de un revisor solo tendrá sentido real cuando haya identidades separadas).

**Importante — cuál es la rama por defecto del repositorio ahora mismo:** al crear el repositorio antes de que existiera ningún commit, GitHub fijó como rama por defecto `claude/secondbrain-architecture-6yur02` (la rama de la sesión de Claude Code que hizo el commit inicial), y no se ha corregido todavía porque ninguna herramienta de las que usamos tiene acceso a esa configuración. **Hasta que Álvaro la cambie manualmente** (`Settings → General → Default branch` → `main`), cualquier acción que dependa de la rama por defecto (un `git clone` sin especificar rama, `create_branch` sin `from_branch` explícito) puede aterrizar en la rama equivocada. Por eso, **toda rama nueva se crea explícitamente desde `main`** (`from_branch=main`, o `git checkout -b <rama> origin/main`), nunca confiando en la rama por defecto.

## Ramas `claude/...`: artefactos de sesión, no ramas de tarea

Las ramas con prefijo `claude/` (como `claude/secondbrain-architecture-6yur02`) las crea automáticamente la herramienta Claude Code para sincronizar una sesión de trabajo con GitHub — son un detalle de esa herramienta, no una convención de este protocolo. No se usan como base de nuevas ramas, no se tratan como ramas de tarea con `Turno`, y no requieren limpieza especial por parte de Grok o Antigravity: se ignoran salvo que Claude indique explícitamente lo contrario.

## Ramas de trabajo

Convención de nombre: `<tipo>/<issue#>-<slug-corto>`

| Prefijo | Cuándo se usa |
|---|---|
| `feat/` | Nueva funcionalidad (cuando ya estemos construyendo producto) |
| `fix/` | Corrección de un bug |
| `research/` | Investigación documentada, sin cambios de comportamiento (ver `docs/04-research/`) |
| `arch/` | Cambios de arquitectura/ADRs sin implicar código de producto |
| `docs/` | Cambios de documentación que no encajan en las categorías anteriores |

Ejemplos: `feat/12-canvas-prototipo`, `arch/7-modelo-de-datos-nodos`, `docs/3-ajustar-workflow`.

Cada rama corresponde a **una única tarea (issue)**. Si durante el trabajo aparece una tarea adicional no prevista, se documenta como una issue nueva en vez de mezclarla en la misma rama/PR.

## Identidad y firma en commits

Con una identidad de GitHub compartida entre los tres agentes (`AGENTS.md`, regla 10), el rastro de autoría depende de una convención, no de una cuenta distinta por agente:

- **Canal de commit por defecto:** clonar y trabajar en rama local + `push` (historial real de git), no la API de contenido de archivo suelto — salvo en vía rápida de un único archivo (ver `TASKS.md`), donde crear/editar el archivo directamente en la rama es aceptable.
- **Trailer de autoría** al final del último commit de la PR, según quién hizo qué: `Implemented-By: Grok`, `Reviewed-By: Antigravity`, `Documented-By: Antigravity`, `Decided-By: Claude`. Si Claude Code añade su propio trailer (`Co-Authored-By: Claude ...`), convive sin problema con el trailer de autoría del agente responsable de esa tarea — no se elimina uno por el otro.
- Esto es además de, no en lugar de, la firma de texto (`— Grok`, etc.) en comentarios y descripciones de issue/PR.

## Pull Requests

- Siempre contra `main`.
- Siempre referencian el issue que las origina (`Closes #N`), usando `.github/PULL_REQUEST_TEMPLATE.md`.
- Se abren cuando el trabajo está listo para revisión, no como borrador permanente de trabajo en curso (si Grok necesita feedback temprano, lo pide explícitamente marcando la PR como borrador y dejándolo claro en la descripción).
- El campo `Turno` en la descripción de la PR indica quién debe actuar a continuación (ver [`WORKFLOW.md`](WORKFLOW.md)).
- Antes de fusionar, Claude comprueba que: Antigravity dio su veredicto (salvo vía rápida, ver `TASKS.md`), no quedan comentarios de revisión sin resolver o sin respuesta, y la PR no introduce una decisión de arquitectura sin su ADR correspondiente.
- Al fusionar, se prefiere **squash merge** para mantener el historial de `main` legible (un commit por tarea), salvo que la PR tenga varios commits que merezca la pena conservar por separado.

## Conflictos y ramas obsoletas

Si una rama de tarea se queda desactualizada respecto a `main` (por ejemplo, porque otra PR se fusionó antes), Grok la actualiza (`merge` o `rebase` de `main` sobre su rama, lo que sea menos disruptivo dado el estado de la rama) antes de pedir revisión de nuevo. Las ramas de tareas ya fusionadas o abandonadas se eliminan para no acumular ruido.
