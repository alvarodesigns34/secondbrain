# Roadmap

> Documento vivo, mantenido por Claude. Las fases son de descubrimiento y validación antes que de features — mientras la visión de producto no esté cerrada ([`VISION.md`](../00-vision/VISION.md)), el roadmap prioriza reducir incertidumbre sobre construir superficie de producto.

## Fase 0 — Cimientos del proyecto y protocolo de colaboración (actual)

**Objetivo:** que Claude, Grok y Antigravity puedan trabajar juntos a través de GitHub sin coordinación manual constante de Álvaro, y que el repositorio documente con claridad qué se construye, cómo se decide y cómo se revisa.

**Entregables:**
- Estructura de documentación (visión, arquitectura, proceso, decisiones).
- Protocolo de trabajo entre los tres agentes (`docs/03-process/`).
- Plantillas de issue/PR y estrategia de ramas.
- Mensajes de incorporación para Grok y Antigravity.

**No incluye:** ninguna funcionalidad de la aplicación.

## Fase 1 — Definición de producto y arquitectura base

**Objetivo:** cerrar (o acotar lo suficiente) las preguntas abiertas de `VISION.md`, y tomar las primeras decisiones técnicas de fondo (ADRs): modelo de datos para nodos/conexiones, enfoque local-first vs. cloud, stack de frontend para la experiencia visual, cómo se integra la IA.

**Cómo se trabaja:** principalmente investigación (`docs/04-research/`) y decisiones (ADRs), con la participación activa de Álvaro para las preguntas de producto. Antigravity presiona sobre los riesgos de cada decisión antes de darla por buena.

**Criterio de salida:** hay un MVP acotado por escrito y suficientes decisiones de arquitectura para empezar a construir sin bloquear al primer prompt.

## Fase 2 — Prototipo del espacio visual

**Objetivo:** validar la experiencia central del producto (el grafo/canvas de nodos) antes de invertir en backend robusto, persistencia definitiva o integración profunda de IA. Un prototipo desechable si hace falta, priorizando aprender rápido sobre construir bien.

## Fase 3 — Modelo de datos real + IA conectada

**Objetivo:** sustituir el prototipo por un modelo de datos sólido y conectar la IA de forma real (no simulada) para organizar, conectar e interpretar nodos.

## Fase 4 — Producto usable de extremo a extremo

**Objetivo:** una versión que Álvaro (y, si aplica, otros usuarios) pueda usar de verdad para organizar su información día a día.

---

Las fases posteriores a la 1 son deliberadamente poco detalladas: se concretan cuando la Fase 1 haya reducido suficiente incertidumbre. Forzar detalle ahora sería fingir certeza que no tenemos.
