# Arquitectura

> Documento vivo, mantenido por Claude. Mientras la visión de producto no esté cerrada ([`VISION.md`](../00-vision/VISION.md)), este documento describe **cómo se toman y se registran las decisiones de arquitectura**, no una arquitectura de aplicación ya definida. Las secciones técnicas están marcadas explícitamente como pendientes.

## Cómo funciona este documento

- Cada decisión de arquitectura no trivial se documenta primero como **ADR** (Architecture Decision Record) en [`decisions/`](decisions), y luego se refleja aquí en forma resumida y siempre actualizada.
- Este archivo describe el estado **actual**; el histórico de cómo llegamos hasta aquí (incluyendo decisiones que ya no aplican) vive en las ADRs, no aquí.
- Cualquier PR que se aparte de lo descrito aquí debe justificarlo explícitamente y, si el cambio es real, ir acompañada de una actualización de este documento y/o una nueva ADR.

## Principios que ya damos por buenos

Estos vienen directamente de la visión de producto y no dependen del stack técnico:

1. **El grafo de nodos es la interfaz principal**, no una vista secundaria — cualquier decisión técnica que obligue a tratarlo como algo accesorio es sospechosa.
2. **La IA es un componente de primera clase**, no una capa añadida al final — el modelo de datos y la arquitectura deben poder exponer contexto a la IA de forma nativa.
3. **Preferimos reversibilidad mientras la visión no esté cerrada**: entre dos opciones técnicas razonables, elegimos la que sea más barata de deshacer si la Fase 1 cambia de rumbo.

## Restricciones de entorno

- El entorno de desarrollo y uso principal de Álvaro es **Windows con PowerShell**, no Linux ni un contenedor. Cualquier propuesta técnica (scripts de build, herramientas de desarrollo) debe priorizar opciones multiplataforma (Node.js/TypeScript, Python, etc.) y justificar explícitamente si en algún punto exige WSL o Docker como requisito obligatorio en lugar de opcional.

## Riesgo conocido a validar en Fase 1 (no decisión cerrada)

Antigravity señaló (auditoría del 2026-09-06, [issue #3](https://github.com/alvarodesigns34/secondbrain/issues/3)) un riesgo real observado en herramientas similares (Obsidian, Roam, Logseq): un grafo visual como interfaz única sufre fricción de captura rápida y se degrada visualmente ("hairball") a partir de varios cientos de nodos. Esto no cambia el principio 1 de arriba, pero es una entrada seria para la Fase 1: ver la pregunta abierta correspondiente en [`VISION.md`](../00-vision/VISION.md). Cualquier decisión al respecto (por ejemplo, una capa de captura rápida desacoplada del lienzo) la toma Álvaro como decisión de producto, no Claude unilateralmente.

## Componentes del sistema (pendientes de decisión)

Ninguna de las siguientes decisiones está tomada. Se resuelven en la Fase 1 (ver [`ROADMAP.md`](../01-roadmap/ROADMAP.md)), como ADRs, y con la investigación previa que haga falta en `docs/04-research/`.

| Componente | Estado | Pregunta clave a resolver |
|---|---|---|
| Modelo de datos (nodos/conexiones) | **Por decidir** | ¿Esquema de tipos de nodo cerrado o modelo genérico tipo grafo de propiedades? |
| Persistencia / almacenamiento | **Por decidir** | ¿Local-first con sincronización, o backend cloud desde el inicio? |
| Frontend / motor visual | **Por decidir** | ¿Qué tecnología soporta bien un canvas/grafo interactivo con buen rendimiento y buena DX? Candidatos a investigar en Fase 1 (ninguno elegido todavía): React Flow, Canvas 2D nativo, PixiJS, Cytoscape.js. |
| Backend / API | **Por decidir** | Depende de la decisión de persistencia. |
| Integración de IA | **Por decidir** | ¿Qué necesita "ver" la IA del grafo para organizar/conectar/interpretar? ¿Dónde vive esa lógica? |
| Autenticación / multi-usuario | **Por decidir** | Depende de si es herramienta personal o producto para terceros (ver preguntas abiertas en `VISION.md`). |

## Registro de decisiones

Ver [`decisions/README.md`](decisions/README.md) para el índice completo de ADRs. La primera ADR ([0001](decisions/0001-sistema-de-colaboracion-entre-agentes.md)) documenta el propio sistema de colaboración entre agentes descrito en `docs/03-process/`.
