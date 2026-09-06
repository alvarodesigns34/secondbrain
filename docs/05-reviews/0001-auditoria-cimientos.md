# 0001 — Auditoría crítica de los cimientos del proyecto

**Fecha:** 2026-09-06  
**Revisor:** Antigravity (Revisor crítico)  
**Turno:** Claude  
**Documentos auditados:**
- Estructura general del repositorio
- `AGENTS.md`
- `STATE.md`
- `docs/00-vision/VISION.md` e `IDEAS.md`
- `docs/01-roadmap/ROADMAP.md`
- `docs/02-architecture/ARCHITECTURE.md` y `docs/02-architecture/decisions/0001-sistema-de-colaboracion-entre-agentes.md`
- `docs/03-process/WORKFLOW.md`, `BRANCHING.md` y `TASKS.md`
- `.github/ISSUE_TEMPLATE/*` y `.github/PULL_REQUEST_TEMPLATE.md`

---

## 1. Veredicto

**CAMBIOS NECESARIOS**

Los cimientos documentales y el diseño conceptual de la colaboración multi-agente son sólidos, maduros y muy superiores a lo habitual en proyectos similares. Sin embargo, existen **ambigüedades operativas en el control de concurrencia**, una **paradoja de permisos en el flujo Git para revisiones/investigaciones**, y **puntos ciegos de arquitectura práctica** que deben clarificarse antes de abrir la Fase 1 y encargar tareas de código a Grok.

---

## 2. Riesgos identificados (con escenarios concretos)

### Riesgo 1: Colisión y desincronización entre el campo `Turno` y `STATE.md` (Doble fuente de verdad)
- **Escenario:** Un issue abierto indica `**Turno:** Grok`, pero en `STATE.md` la tabla indica `Turno: Claude` (porque una sesión previa de Claude se interrumpió o no sincronizó ambos archivos). Grok lee `AGENTS.md` (Regla 9: *"`STATE.md` es la fuente de verdad del ahora mismo"*) y decide esperar. Al mismo tiempo, `TASKS.md` dice: *"Regla de oro: un agente solo actúa si el campo Turno en el issue/PR lo señala a él"*.
- **Impacto:** Bloqueo silencioso (*deadlock*) o ejecución desalineada. Al haber dos fuentes de verdad para el turno actual, cualquier fallo humano o de agente al actualizar una de ellas rompe la máquina de estados.
- **Riesgo multi-tarea:** El formato actual de `STATE.md` asigna una sola celda a cada agente (`| Grok | Turno actual |`). Si en Fase 1 se lanzan 2 investigaciones en paralelo o una feature y un bugfix, es imposible reflejar el estado sin romper la tabla.

### Riesgo 2: La paradoja de `main` protegida frente a entregables documentales fuera de PR
- **Escenario:** `AGENTS.md` (Regla 1) establece: *"Nadie hace push directo a `main`. Todo cambio entra por Pull Request"*, y la Regla 2 añade que sólo Claude fusiona. Sin embargo, `ANTIGRAVITY.md` (línea 49) y `docs/05-reviews/README.md` indican que Antigravity documenta auditorías generales como un archivo en `docs/05-reviews/` y abre un issue con `Turno: Claude`. Lo mismo aplica a investigaciones libres en `docs/04-research/`.
- **Impacto:** Si Antigravity o Grok hacen push directo a `main` para dejar su archivo de revisión o investigación, violan la regla constitucional no negociable #1. Si abren una PR para subir un simple markdown de revisión, se genera un bucle absurdo: ¿quién revisa la PR de la revisión? ¿Claude tiene que hacer merge de la auditoría antes de poder debatirla en un issue?
- **Riesgo:** Parálisis del flujo o incumplimiento forzado de las reglas en la primera acción de Antigravity fuera de una PR de código.

### Riesgo 3: Identidad de GitHub compartida y ausencia de barreras técnicas
- **Escenario:** Todos los agentes operan actualmente bajo la misma credencial / cuenta de GitHub (`@alvarodesigns34`). No hay separación criptográfica de identidades ni branch protection activo (ambas ramas tienen `protected: false`).
- **Impacto:** Cualquier agente con acceso a la API o al repo puede, por una alucinación o instrucción malinterpretada, hacer un force-push a `main`, autoaprobarse una PR o borrar ramas. El cumplimiento del protocolo descansa al 100% en la disciplina del prompt, sin salvaguardas en el servidor.

### Riesgo 4: Trampas de usabilidad y rendimiento en el dogma "Visual-First / Grafo como interfaz principal"
- **Escenario:** `VISION.md` y `ARCHITECTURE.md` establecen como principio: *"El grafo de nodos es la interfaz principal, no una vista secundaria"*. En la práctica de Second Brains (Obsidian, Roam, Logseq), los grafos visuales 2D interactivos sufren dos problemas letales:
  1. *Fricción extrema de captura:* Capturar un pensamiento rápido, una tarea de 5 segundos o un enlace desde el móvil en un lienzo visual de nodos interconectados es frustrante frente a una interfaz de captura rápida lineal (quick-capture / inbox).
  2. *Escalabilidad gráfica:* A partir de 500-1.000 nodos, los motores de fuerza 2D (force-directed) en DOM/Canvas degradan rendimiento, generan marañas ininteligibles ("hairballs") y pierden utilidad cognitiva si no hay clusterización semántica jerárquica estricta.
- **Impacto:** Riesgo de construir una demo visual espectacular pero un producto inutilizable en el día a día para la gestión real de proyectos e ideas.

### Riesgo 5: Indefinición del entorno operativo y tecnológico de contorno
- **Escenario:** `ARCHITECTURE.md` marca el 100% de los componentes técnicos como "Por decidir". Aunque esto es correcto para Fase 0, no se han explicitado las restricciones de contorno del fundador (Álvaro trabaja en Windows con PowerShell, no en un entorno Linux containerizado).
- **Impacto:** Grok o Claude podrían proponer arquitecturas o scripts de build que asuman utilidades bash Unix, Docker daemons pesados o dependencias no portables, provocando fricción innecesaria en la máquina local.

---

## 3. Debe corregirse antes de arrancar Fase 1

1. **Definir la jerarquía estricta de fuentes de verdad:**
   - Enmendar `AGENTS.md` (Regla 9) y `TASKS.md` para clarificar:
     - **Nivel micro (ejecución):** El bloque de metadatos en el cuerpo del issue/PR (`**Turno:** ...`) es la **única autoridad operativa** para saber quién debe actuar en esa tarea concreta.
     - **Nivel macro (resumen):** `STATE.md` es un cuadro de mando informativo de alto nivel gestionado exclusivamente por Claude. En caso de discrepancia temporal, manda siempre el issue/PR.
2. **Resolver formalmente el protocolo de publicación documental (`docs/04-research/` y `docs/05-reviews/`):**
   - Especificar en `BRANCHING.md` y `AGENTS.md` una de estas dos soluciones:
     - *Opción A (Recomendada):* Las ramas de tipo `docs/` o `research/` abiertas por Antigravity o Grok que solo añadan archivos dentro de `docs/04-research/` o `docs/05-reviews/` son fusionables directamente por Claude sin requerir ciclo de revisión formal previo.
     - *Opción B:* Autorizar push directo a `main` **exclusivamente** para archivos dentro de `docs/04-research/` y `docs/05-reviews/`, manteniendo prohibido el push directo en cualquier otra ruta.
3. **Evolucionar la tabla de turnos de `STATE.md` a modelo multi-tarea:**
   - Modificar la tabla en `STATE.md` para soportar tareas en paralelo:
     ```markdown
     | Tarea / Issue | Agente asignado | Turno actual | Estado |
     |---|---|---|---|
     | #1 Cimientos | Claude | Claude | En revisión |
     ```
4. **Incorporar protocolo de "Vía Rápida" (Fast-Track) para cambios triviales:**
   - Evitar que una errata, un enlace roto o un ajuste cosmético de documentación requiera el ciclo completo de 4 pasos entre 3 agentes. Claude debe poder ejecutar y mergear cambios triviales de forma directa.
5. **Añadir restricciones de entorno anfitrión en `ARCHITECTURE.md`:**
   - Documentar que el entorno primario de ejecución y desarrollo es Windows (PowerShell), priorizando tecnologías cross-platform (Node.js/TypeScript, Vite, Python, Tauri/Electron) que no exijan emulación WSL forzada salvo acuerdo expreso.

---

## 4. Sugerencias opcionales (no bloqueantes)

1. **Integrar GitHub Actions de integridad documental en Fase 0:**
   - Crear un workflow mínimo (`.github/workflows/docs-check.yml`) que ejecute verificación de enlaces markdown (`markdown-link-check`) y validación de sintaxis. Previene enlaces rotos como los observados en referencias relativas de la plantilla de issues.
2. **Crear plantilla de reporte de errores (`.github/ISSUE_TEMPLATE/bug-report.md`):**
   - El repositorio cuenta con plantillas para tareas de Grok y hallazgos de Antigravity, pero carece de plantilla para fallos funcionales que surjan durante el prototipado.
3. **Mecanismo de tiempo límite / desatasco (Watchdog):**
   - Si un issue con `Turno: Grok` o `Turno: Antigravity` permanece inactivo sin avance, Claude o Álvaro deben tener la potestad documentada de reclamar el turno (`Turno: Claude`) sin esperar confirmación para desbloquear el flujo.
4. **Limpieza automática de ramas:**
   - Activar la opción nativa de GitHub *"Automatically delete head branches"* tras el merge de PRs para mantener el repositorio limpio.

---

## 5. Preguntas para Claude (Arquitectura y Proceso)

1. **Captura rápida vs. Grafo:** ¿Se contempla en la arquitectura una capa de *Quick Capture* / Inbox lineal desacoplada del lienzo gráfico, para que la entrada de información no dependa de la navegación en el grafo?
2. **Privacidad y modelo de datos:** Para un Second Brain personal, ¿consideramos el enfoque *Local-First* (datos en máquina de Álvaro, SQLite/DuckDB + sincronización cifrada opcional) como un pilar arquitectónico de partida, o se contempla una arquitectura Cloud SaaS tradicional?
3. **Saneamiento de ramas iniciales:** La rama `claude/secondbrain-architecture-6yur02` quedó huérfana en el repositorio con el mismo commit que `main`. ¿Debe cerrarse/eliminarse formalmente para evitar confusiones de contexto?
4. **Estrategia técnica para el prototipo de Fase 2:** Antes de saltar a librerías de grafos pesadas, ¿qué opciones de visualización (ej. React Flow, Canvas API 2D nativo, PixiJS, Cytoscape) consideras más reversibles y ligeras para validar el concepto?

---

## 6. Qué está bien hecho (Fortalezas destacadas)

1. **Separación de poderes y jerarquía inequívoca (`AGENTS.md`):** La asignación de roles es impecable. Evita el patrón disfuncional de "comité de agentes que votan" asignando la autoridad técnica y de coherencia a Claude y el arbitraje final de producto a Álvaro.
2. **Pragmatismo de comunicación asíncrona:** Basar toda la coordinación en primitivas nativas de GitHub (Issues, PRs, Markdown) sin depender de servidores de sockets, webhooks o herramientas externas es la decisión más sensata y resiliente para el estado actual de la tecnología de agentes.
3. **Resistencia a la codificación prematura:** Respetar la Fase 0 sin escribir código de producto hasta que los cimientos documentales y de colaboración estén completamente asentados ahorra semanas de refactorizaciones caóticas.
4. **Trazabilidad temprana mediante ADRs:** Establecer el registro de decisiones de arquitectura desde el primer momento (`ADR-0001`) asegura que las decisiones no se perderán en historiales volátiles de chat.
