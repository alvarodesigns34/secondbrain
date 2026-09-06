# Visión de producto

> Documento vivo. La visión de producto **no está cerrada todavía** — esto es un punto de partida, no una especificación. Se actualiza a medida que Álvaro resuelve las preguntas abiertas de más abajo. Cualquier agente puede proponer cambios, pero la decisión final sobre visión de producto es de Álvaro, y Claude la mantiene coherente aquí.

## La idea de partida

Un **AI Command Center / Second Brain**: una herramienta para organizar información, proyectos, tareas, documentos, ideas, conocimiento y otros elementos de la vida digital, a través de un **espacio visual de conexiones**.

- Los elementos se representan como **nodos** relacionados entre sí (no listas, no carpetas jerárquicas como forma principal de organización).
- La **IA tiene un papel central**: organiza, conecta, interpreta y trabaja con la información, no es una función añadida.
- La experiencia debe sentirse **mucho más visual y especial** que una app de productividad tradicional (tipo Notion/Todoist), no un formulario con IA encima.

## Pilares de producto (de partida, sujetos a revisión)

1. **Visual-first**: el grafo de nodos es la interfaz principal, no una vista secundaria.
2. **IA como colaborador activo**, no como buscador o autocompletado: propone conexiones, resume, reorganiza, interpreta.
3. **Todo tipo de información cabe dentro**: proyectos, tareas, documentos, ideas, conocimiento — sin que el usuario tenga que elegir "en qué app va esto".

## Explícitamente fuera de alcance por ahora

- No estamos construyendo la aplicación todavía (Fase 0, ver [`ROADMAP.md`](../01-roadmap/ROADMAP.md)).
- No hay stack tecnológico decidido — ver huecos "por decidir" en [`ARCHITECTURE.md`](../02-architecture/ARCHITECTURE.md).
- No se asume todavía si esto es una herramienta personal de Álvaro o un producto para terceros.

## Preguntas abiertas (a resolver por Álvaro, con Claude ayudando a estructurar la decisión)

Estas preguntas condicionan directamente la arquitectura y el alcance del MVP. Mientras no tengan respuesta, evitamos comprometernos a decisiones técnicas que dependan de ellas.

1. **Usuario objetivo**: ¿herramienta personal para uso propio, o producto pensado para que lo use otra gente desde el principio? Esto afecta a auth, multi-usuario, hosting, y a cuánto esfuerzo dedicar a onboarding/UX pulida desde ya.
2. **Qué es un "nodo"**: ¿tipos de nodo cerrados (nota, tarea, documento, proyecto, persona...) o un modelo genérico donde cualquier cosa es un nodo con propiedades? Esto es una decisión de modelo de datos con implicaciones fuertes en arquitectura.
3. **Naturaleza del espacio visual**: ¿grafo de nodos tipo force-directed en 2D, canvas libre estilo pizarra, algo más estructurado (vistas tipo mapa mental)? ¿Es la única forma de navegar la información o convive con vistas más tradicionales (lista, búsqueda)? Relacionado — Antigravity señaló (auditoría del 2026-09-06) un riesgo conocido en herramientas similares (Obsidian, Roam, Logseq): el grafo visual como interfaz única genera fricción para capturar algo rápido (una tarea de 5 segundos) y se degrada visualmente ("hairball") a partir de varios cientos de nodos. ¿El grafo convive con una capa de captura rápida lineal (inbox) desacoplada del lienzo, o debe poder con la captura rápida también? Ver [`ARCHITECTURE.md`](../02-architecture/ARCHITECTURE.md#riesgo-conocido-a-validar-en-fase-1-no-decisión-cerrada).
4. **Papel exacto de la IA**: ¿sugiere conexiones y el usuario decide, las crea de forma autónoma, responde preguntas sobre el contenido, genera contenido nuevo, todo lo anterior? ¿Hay una única IA "curadora" o varios agentes con funciones distintas dentro del propio producto?
5. **Local-first vs. cloud**: ¿los datos viven localmente con sincronización opcional, o es una app cloud desde el inicio? Afecta directamente a privacidad, arquitectura de datos y complejidad de sincronización.
6. **Alcance del MVP**: de todo lo anterior, ¿qué es lo mínimo que hay que demostrar primero para validar que la experiencia central (el espacio visual + IA) funciona y merece la pena seguir invirtiendo?
7. **Modelo de distribución/negocio** (si aplica): ¿gratuito/personal, producto de pago, open source? No bloquea el trabajo técnico inmediato, pero conviene tenerlo en mente pronto.

## Cómo se actualiza este documento

- Álvaro aporta ideas y decisiones en conversación con Claude (o directamente aquí).
- Las ideas sueltas que todavía no están maduras para entrar en la visión formal se apuntan en [`IDEAS.md`](IDEAS.md) y Claude las revisa periódicamente.
- Cuando una pregunta abierta se resuelve, Claude la mueve de "Preguntas abiertas" a la sección correspondiente de arriba y, si tiene implicaciones técnicas, abre un ADR en `docs/02-architecture/decisions/`.
