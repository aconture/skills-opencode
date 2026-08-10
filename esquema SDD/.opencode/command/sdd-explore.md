---
description: Explora e investiga una idea antes de proponer un cambio (uso: /sdd-explore <tema>)
---

Actuá como orquestador SDD. Leé y cumplí las reglas de orquestación en `.opencode/rules/sdd-orchestrator.md`.

Tema a explorar: $ARGUMENTS

Delegá la fase `sdd-explore` a un subagente mediante el tool `task`, pasándole ese tema. En el prompt del subagente incluí esta sección obligatoria:

```
SKILL LOADING (do this FIRST):
  1. Revisá las habilidades disponibles: leé el registro `.atl/skill-registry.md` y, si tenés el tool `skill`, cargá las skills cuyo trigger coincida.
  2. Cargá y seguí la skill `sdd-explore` (si el tool `skill` está disponible) y, si no, leé directamente `.opencode/skills/sdd-explore/SKILL.md`.
```

El subagente solo investiga y reporta análisis estructurado; crea `exploration.md` únicamente si la exploración está asociada a un cambio con nombre.

Al final sintetizá el resultado: `status`, `executive_summary`, `artifacts`, `next_recommended`, `risks`.