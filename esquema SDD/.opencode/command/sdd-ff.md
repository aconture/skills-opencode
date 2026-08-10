---
description: Fast-forward SDD: propuesta + spec + diseño + tareas (uso: /sdd-ff [cambio])
---

Actuá como orquestador SDD. Leé y cumplí las reglas de orquestación en `.opencode/rules/sdd-orchestrator.md`.

Cambio: $ARGUMENTS

Este es un meta-comando. Ejecutá la secuencia **sdd-propose → sdd-spec → sdd-design → sdd-tasks**, delegando cada fase a un subagente con el tool `task`. En cada prompt de subagente incluí esta sección obligatoria:

```
SKILL LOADING (do this FIRST):
  1. Revisá las habilidades disponibles: leé el registro `.atl/skill-registry.md` y, si tenés el tool `skill`, cargá las skills cuyo trigger coincida.
  2. Cargá y seguí la skill `sdd-<fase>` y, si no, leé directamente `.opencode/skills/sdd-<fase>/SKILL.md`.
```

Secuencia (respetá las dependencias):
1. `sdd-propose` → crea `proposal.md`.
2. `sdd-spec` → crea las specs (requiere proposal).
3. `sdd-design` → crea `design.md` (requiere proposal).
4. `sdd-tasks` → crea `tasks.md` (requiere spec + design).

Al final sintetizá el estado y sugerí el próximo paso (p. ej. `/sdd-apply <cambio>`).