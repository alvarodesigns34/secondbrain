# Registro de decisiones de arquitectura (ADRs)

Cada decisión técnica o de producto no trivial se documenta aquí como un archivo numerado secuencialmente: `NNNN-titulo-corto.md`. Usa [`TEMPLATE.md`](TEMPLATE.md) como base.

**Quién puede proponer una ADR:** cualquiera (Claude, Grok, Antigravity). **Quién la confirma como `Aceptada`:** siempre Claude, aunque el origen de la propuesta sea de otro agente.

Una ADR nunca se borra ni se reescribe con el tiempo: si una decisión cambia, se crea una ADR nueva que la sustituye, y la antigua se marca como `Sustituida por NNNN`.

## Índice

| # | Título | Estado | Autor |
|---|---|---|---|
| [0001](0001-sistema-de-colaboracion-entre-agentes.md) | Sistema de colaboración entre agentes | Aceptada | Claude |
| [0002](0002-correcciones-de-protocolo-tras-primera-auditoria.md) | Correcciones de protocolo tras la primera auditoría cruzada | Aceptada | Claude (a partir de hallazgos de Grok y Antigravity) |
