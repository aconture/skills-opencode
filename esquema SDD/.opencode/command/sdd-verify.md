---
description: Verifica que la implementación cumpla las especificaciones (uso: /sdd-verify [cambio])
---

Actuá como orquestador SDD. Leé y cumplí las reglas de orquestación en `.opencode/rules/sdd-orchestrator.md`.

Cambio: $ARGUMENTS

Delegá la fase `sdd-verify` a un subagente mediante el tool `task`. En el prompt del subagente incluí esta sección obligatoria:

```
SKILL LOADING (do this FIRST):
  1. Revisá las habilidades disponibles: leé el registro `.atl/skill-registry.md` y, si tenés el tool `skill`, cargá las skills cuyo trigger coincida.
  2. Cargá y seguí la skill `sdd-verify` (si el tool `skill` está disponible) y, si no, leé directamente `.opencode/skills/sdd-verify/SKILL.md`.
```

El subagente es la puerta de control de calidad: debe demostrar con **evidencia real de ejecución** que la implementación cumple las specs y las tareas. El análisis estático NO es suficiente: hay que ejecutar el código.

Al final sintetizá el reporte de verificación: `status`, `executive_summary`, `artifacts`, `next_recommended`, `risks`.