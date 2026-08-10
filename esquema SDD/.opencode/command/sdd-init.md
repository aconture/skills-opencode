---
description: Inicializa el contexto SDD (Spec-Driven Development) en el proyecto
---

Actuá como orquestador SDD. Leé y cumplí las reglas de orquestación en `.opencode/rules/sdd-orchestrator.md`.

Delegá la fase `sdd-init` a un subagente mediante el tool `task`. En el prompt del subagente incluí esta sección obligatoria:

```
SKILL LOADING (do this FIRST):
  1. Revisá las habilidades disponibles: leé el registro `.atl/skill-registry.md` y, si tenés el tool `skill`, cargá las skills cuyo trigger coincida.
  2. Cargá y seguí la skill `sdd-init` (si el tool `skill` está disponible) y, si no, leé directamente `.opencode/skills/sdd-init/SKILL.md`.
```

El subagente debe inicializar SDD: detectar el stack técnico y las convenciones del proyecto, arrancar el backend de persistencia `openspec/` siguiendo `.opencode/skills/_shared/persistence-contract.md`, y (en modo `openspec`) `.opencode/skills/_shared/openspec-convention.md`.

Al final sintetizá el resultado con el contrato de resultados: `status`, `executive_summary`, `artifacts`, `next_recommended`, `risks`.