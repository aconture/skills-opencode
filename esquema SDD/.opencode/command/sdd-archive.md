---
description: Archiva un cambio completado y sincroniza las specs delta (uso: /sdd-archive [cambio])
---

Actuá como orquestador SDD. Leé y cumplí las reglas de orquestación en `.opencode/rules/sdd-orchestrator.md`.

Cambio: $ARGUMENTS

Delegá la fase `sdd-archive` a un subagente mediante el tool `task`. En el prompt del subagente incluí esta sección obligatoria:

```
SKILL LOADING (do this FIRST):
  1. Revisá las habilidades disponibles: leé el registro `.atl/skill-registry.md` y, si tenés el tool `skill`, cargá las skills cuyo trigger coincida.
  2. Cargá y seguí la skill `sdd-archive` (si el tool `skill` está disponible) y, si no, leé directamente `.opencode/skills/sdd-archive/SKILL.md`.
```

El subagente debe fusionar las specs delta con las specs principales (fuente de verdad) y mover la carpeta del cambio al archivo, completando el ciclo SDD.

Al final sintetizá el reporte de archivado: `status`, `executive_summary`, `artifacts`, `next_recommended`, `risks`.