# Flujo de trabajo entre agentes

Este documento describe, paso a paso, cómo trabajamos Claude, Grok, Antigravity y Álvaro. Si tienes dudas sobre "qué hago ahora" en un rol concreto, este es el documento a consultar.

## El bucle completo

```
Claude especifica una tarea (issue)
        │  Turno: Grok
        ▼
Grok implementa (rama + commits + PR)
        │  Turno: Antigravity
        ▼
Antigravity revisa (comentarios + veredicto en la PR)
        │
        ├── Cambios necesarios ──► Turno: Grok (vuelve arriba)
        │
        └── Aprobado ──► Turno: Claude
                              │
                              ▼
                  Claude analiza el resultado completo,
                  decide si fusiona, pide más cambios,
                  o abre una nueva tarea a partir de lo aprendido
                              │
                              ▼
                     PR fusionada a `main`,
                  issue cerrado, STATE.md actualizado
```

El ciclo puede repetirse varias veces sobre la misma PR (Grok corrige, Antigravity vuelve a revisar) antes de llegar a Claude. Eso es normal y esperado.

Para cambios triviales (typos, enlaces rotos, o un archivo nuevo e independiente en `docs/04-research/`/`docs/05-reviews/`) existe una **vía rápida** que salta el paso de Antigravity — ver [`TASKS.md`](TASKS.md#vía-rápida-fast-track-para-cambios-triviales). No aplica a nada que toque `AGENTS.md`, `ARCHITECTURE.md`, una ADR, o el propio `docs/03-process/`.

## Antes de actuar: checklist de arranque de sesión

Ningún agente (Claude incluido) espera a que Álvaro le avise de que hay trabajo. Al empezar cualquier sesión: (1) leer `STATE.md`, (2) listar issues abiertos con `Turno: <tú>`, (3) listar PRs abiertas con `Turno: <tú>`, (4) actuar solo sobre esos — si no hay ninguno, no inventar trabajo. Detalle completo en [`TASKS.md`](TASKS.md#checklist-de-arranque-de-sesión-todos-los-agentes). Si un turno lleva bloqueado sin justificación, Claude o Álvaro pueden reclamarlo (`AGENTS.md`, regla 11).

## Rol por rol

### Cómo crea Claude una tarea

1. Antes de crear una tarea, Claude confirma que hay contexto suficiente en `VISION.md`/`ARCHITECTURE.md` para especificarla sin ambigüedad; si no lo hay, primero resuelve esa ambigüedad con Álvaro.
2. Abre un **GitHub Issue** en `secondbrain` usando la plantilla `.github/ISSUE_TEMPLATE/task-for-grok.md`.
3. El issue incluye: contexto, objetivo, alcance (qué entra y qué no), restricciones de arquitectura, criterios de aceptación y definición de "hecho". Ver el detalle de la plantilla en [`TASKS.md`](TASKS.md).
4. El issue se abre con `Turno: Grok`.
5. Claude actualiza `STATE.md` para reflejar la nueva tarea en progreso.

### Cómo recibe e implementa Grok una tarea

1. Grok solo actúa sobre issues con `Turno: Grok`.
2. Lee el issue completo y los documentos que enlaza (arquitectura, ADRs relevantes). Si algo es ambiguo o contradictorio, lo señala como comentario en el propio issue y cambia `Turno: Claude` en vez de asumir una interpretación — no se adivina la intención cuando hay ambigüedad real de alcance o arquitectura.
3. Crea una rama siguiendo la convención de [`BRANCHING.md`](BRANCHING.md) (`feat/<issue#>-slug`, etc.).
4. Implementa, con commits que referencian el issue (`#N`).
5. Abre una **Pull Request** contra `main`, usando `.github/PULL_REQUEST_TEMPLATE.md`, referenciando el issue (`Closes #N`).
6. Deja constancia en la PR de: qué cambia y por qué, cómo se probó, limitaciones conocidas, y cualquier decisión técnica no trivial tomada durante la implementación (si la hay, propone si merece una ADR).
7. Cambia `Turno: Antigravity` en la PR.

### Cómo revisa Antigravity

1. Antigravity solo actúa sobre PRs (o issues) con `Turno: Antigravity`, o cuando Álvaro le pide explícitamente mirar algo puntual.
2. Evalúa: corrección, alineación con `ARCHITECTURE.md` y las ADRs vigentes, riesgos, complejidad innecesaria, deuda técnica, seguridad, coherencia con `VISION.md`.
3. Deja su revisión **en la propia PR** (comentarios de GitHub, y si tiene esa capacidad, una revisión nativa con veredicto de aprobación/cambios solicitados). El resumen sigue siempre esta estructura fija:
   - **Veredicto:** Aprobado / Cambios necesarios / Bloqueante.
   - **Riesgos.**
   - **Debe corregirse antes de fusionar.**
   - **Sugerencias opcionales** (no bloquean).
   - **Preguntas para Claude** (si el problema es de arquitectura/producto, no de implementación).
   - **Qué está bien hecho** (para que la revisión sea calibrada, no solo negativa).
4. Si la revisión no está ligada a una PR concreta (una auditoría libre, por ejemplo la primera pasada sobre estos cimientos), se documenta como archivo en `docs/05-reviews/AAAA-MM-DD-tema.md` con la misma estructura.
5. Cambia `Turno: Grok` si hay cambios que hacer, o `Turno: Claude` si aprueba o si el hallazgo es de arquitectura/producto y no de implementación.

### Cómo decide Claude el siguiente paso

1. Claude solo actúa cuando le corresponde el turno, o cuando Álvaro le pide explícitamente revisar algo.
2. Lee la PR completa: el código/cambio, la revisión de Antigravity, y cualquier pregunta abierta dirigida a Claude.
3. Decide una de estas acciones y la ejecuta sin necesidad de que Álvaro apruebe cada paso rutinario:
   - **Fusionar** la PR a `main` (si Antigravity aprobó y Claude no ve objeciones de arquitectura/producto).
   - **Pedir más cambios** (vuelve a Grok con instrucciones concretas).
   - **Zanjar un desacuerdo** entre Grok y Antigravity, explicando la decisión.
   - **Abrir una ADR** si la PR reveló una decisión de arquitectura que merece quedar registrada.
   - **Abrir una nueva tarea** si de la implementación surge trabajo adicional.
4. Tras fusionar, cierra el issue (si no se cerró automáticamente) y actualiza `STATE.md`: mueve la tarea a "recientemente completado", actualiza "en progreso", y propone el siguiente paso.
5. Si la decisión tiene implicaciones de producto (no solo técnicas), Claude se lo plantea a Álvaro en vez de decidir unilateralmente.

## Cómo interactúa Álvaro (el fundador como mensajero y decisor)

Álvaro no tiene por qué operar GitHub directamente ni coordinar cada paso. Interactúa con Claude en lenguaje natural, y Claude traduce eso al protocolo:

| Álvaro dice | Qué hace Claude |
|---|---|
| **"Quiero que hagamos esto [idea]"** | La evalúa contra `VISION.md`/`ROADMAP.md`, y si tiene sentido ahora, la convierte en una o varias tareas (issues) para Grok, o la apunta en `docs/00-vision/IDEAS.md` si no es el momento todavía. |
| **"Seguid" / "continúa"** | Revisa `STATE.md` y las issues/PRs abiertas, y ejecuta el siguiente paso rutinario que le corresponda según el turno, sin pedir permiso para pasos ya cubiertos por este protocolo. |
| **"Mira lo que ha hecho Grok"** | Revisa la(s) PR(s) abiertas de Grok, decide si están listas para pasar a Antigravity o si necesitan ajustes antes, y actualiza el turno. |
| **"Antigravity cree que X está mal"** (Álvaro pega texto de otra conversación) | Localiza la PR/issue relevante; si la revisión de Antigravity no está ya en GitHub, la vuelca ahí (como comentario o en `docs/05-reviews/`) antes de actuar, para no perder el rastro; evalúa si tiene razón y decide la acción (nueva tarea, ajuste de arquitectura/ADR, o descartar la objeción explicando por qué). |
| **"¿Qué hacemos ahora?"** | Resume el estado desde `STATE.md` y propone un siguiente paso concreto y accionable. |

Cuando Álvaro actúa de mensajero entre agentes (pega texto de Grok o Antigravity que no llegó a GitHub), la regla es siempre la misma: **antes de actuar sobre esa información, queda registrada en GitHub** (comentario en el issue/PR correspondiente, o archivo en `docs/05-reviews/`), para que no se pierda entre conversaciones separadas y cualquier agente futuro pueda encontrarla.

## Cómo evitamos pisarnos el trabajo

- Cada agente **solo actúa donde tiene el turno** (ver campo `Turno` en cada issue/PR; si hay PR abierta, manda la PR — ver `TASKS.md`).
- **Una rama por tarea**, creada explícitamente desde `main` (nunca desde la rama por defecto del repo ni desde ramas `claude/...` — ver `BRANCHING.md`).
- **Nadie más que Claude fusiona a `main`**, y nunca con push directo (ni siquiera en vía rápida).
- `STATE.md` refleja en todo momento quién tiene el turno en qué, para poder verlo de un vistazo sin recorrer todas las issues — pero ante cualquier discrepancia, manda el issue/PR, no `STATE.md`.
- Cada comentario, issue o PR termina con una firma (`— Grok` / `— Claude` / `— Antigravity` / `— Álvaro`) para que quede claro quién dijo qué con una identidad de GitHub compartida.
