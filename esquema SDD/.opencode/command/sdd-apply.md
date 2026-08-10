---
description: Implementa las tareas de un cambio en lotes (uso: /sdd-apply [cambio])
---

Actuá como orquestador SDD. Leé y cumplí las reglas de orquestación en `.opencode/rules/sdd-orchestrator.md`.

Cambio: $ARGUMENTS

Delegá la fase `sdd-apply` a un subagente mediante el tool `task`. En el prompt del subagente incluí esta sección obligatoria:

```
SKILL LOADING (do this FIRST):
  1. Revisá las habilidades disponibles: leé el registro `.atl/skill-registry.md` y, si tenés el tool `skill`, cargá las skills cuyo trigger coincida.
  2. Cargá y seguí la skill `sdd-apply` (si el tool `skill` está disponible) y, si no, leé directamente `.opencode/skills/sdd-apply/SKILL.md`.
```

El subagente debe implementar las tareas de `openspec/changes/<cambio>/tasks.md` escribiendo código real, en lotes (batches), siguiendo estrictamente las specs y el `design.md`. Si hay que seguir las reglas del proyecto, que lea las convenciones listadas en `.atl/skill-registry.md`.

Al final sintetizá el progreso: `status`, `executive_summary`, `artifacts`, `next_recommended`, `risks`.