# Second Brain — AI Command Center

Este repositorio es el espacio de trabajo compartido de un equipo de tres agentes de IA y un fundador humano. Es, a la vez, el código del producto y la memoria del proceso: aquí vive la visión, las decisiones, las tareas y las revisiones, no solo la aplicación.

## Qué estamos construyendo

Una aplicación tipo **AI Command Center / Second Brain**: un espacio visual donde la información personal (proyectos, tareas, documentos, ideas, conocimiento) se representa como **nodos conectados entre sí**, con una IA que ayuda a organizar, conectar, interpretar y trabajar con esa información.

La visión de producto todavía **no está cerrada**. No estamos construyendo la aplicación todavía. Lee primero:

- **[`docs/00-vision/VISION.md`](docs/00-vision/VISION.md)** — qué queremos construir y qué preguntas siguen abiertas.
- **[`docs/01-roadmap/ROADMAP.md`](docs/01-roadmap/ROADMAP.md)** — en qué fase estamos.

## Quién trabaja aquí

| Quién | Rol | Responsabilidad principal |
|---|---|---|
| **Álvaro** | Fundador / Director | Visión de producto, ideas, decisiones importantes, dirección general |
| **Claude** | Líder / Arquitecto | Dirección técnica, arquitectura, reparto de tareas, prioridades, coherencia global, decide qué se incorpora |
| **Grok** | Lead Developer | Implementación: código, features, fixes, rendimiento |
| **Antigravity** | Revisor crítico | Revisión de arquitectura, código y decisiones; segunda opinión exigente |

La jerarquía es clara: **Claude dirige y decide, Grok implementa, Antigravity audita, Álvaro marca el rumbo.** El detalle completo de roles y reglas está en **[`AGENTS.md`](AGENTS.md)**.

## Por dónde empezar

1. **[`AGENTS.md`](AGENTS.md)** — quiénes somos, jerarquía, reglas no negociables.
2. **[`STATE.md`](STATE.md)** — estado actual del proyecto, en qué está trabajando cada quién, próximo paso.
3. **[`docs/03-process/WORKFLOW.md`](docs/03-process/WORKFLOW.md)** — cómo trabajamos los tres agentes, paso a paso.
4. **[`docs/agent-briefs/`](docs/agent-briefs)** — los mensajes de incorporación para Grok y Antigravity.

## Estructura del repositorio

```
secondbrain/
├── AGENTS.md                        Constitución del equipo: roles, jerarquía, reglas
├── CLAUDE.md                        Puntero rápido para sesiones de Claude Code
├── STATE.md                         Estado vivo del proyecto (lo mantiene Claude)
├── docs/
│   ├── 00-vision/
│   │   ├── VISION.md                Qué construimos y qué sigue abierto
│   │   └── IDEAS.md                 Bandeja de entrada de ideas sueltas de Álvaro
│   ├── 01-roadmap/
│   │   └── ROADMAP.md               Fases del proyecto y qué toca ahora
│   ├── 02-architecture/
│   │   ├── ARCHITECTURE.md          Arquitectura vigente (vivo, con huecos "por decidir")
│   │   └── decisions/               Registro de decisiones (ADRs)
│   ├── 03-process/
│   │   ├── WORKFLOW.md              El bucle Claude → Grok → Antigravity → Claude
│   │   ├── BRANCHING.md             Estrategia de ramas y Pull Requests
│   │   └── TASKS.md                 Cómo se crean y gestionan las tareas
│   ├── 04-research/                 Investigación libre de cualquier agente
│   ├── 05-reviews/                  Auditorías de Antigravity no ligadas a una PR concreta
│   └── agent-briefs/
│       ├── GROK.md                  Mensaje de incorporación para Grok (copiar/pegar)
│       └── ANTIGRAVITY.md           Mensaje de incorporación para Antigravity (copiar/pegar)
└── .github/
    ├── ISSUE_TEMPLATE/              Plantillas de tarea y de hallazgo de revisión
    └── PULL_REQUEST_TEMPLATE.md     Plantilla de Pull Request
```

No hay todavía código de aplicación: esta fase (**Fase 0**) construye los cimientos del proyecto y el protocolo de colaboración, no el producto.
