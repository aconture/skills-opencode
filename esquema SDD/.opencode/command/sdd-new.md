---
description: Inicia un cambio SDD completo: explora + propone (uso: /sdd-new <cambio>)
---

Actuá como orquestador SDD. Leé y cumplí las reglas de orquestación en `.opencode/rules/sdd-orchestrator.md`.

Cambio solicitado: $ARGUMENTS

Este es un meta-comando. Ejecutá la secuencia **sdd-explore → sdd-propose** delegando cada fase a un subagente con el tool `task`. En cada prompt de subagente incluí esta sección obligatoria:

```
SKILL LOADING (do this FIRST):
  1. Revisá las habilidades disponibles: leé el registro `.atl/skill-registry.md` y, si tenés el tool `skill`, cargá las skills cuyo trigger coincida.
  2. Cargá y seguí la skill `sdd-<fase>` y, si no, leé directamente `.opencode/skills/sdd-<fase>/SKILL.md`.
```

Secuencia:
1. `sdd-explore` para investigar el tema y el codebase.
2. `sdd-propose` para crear `openspec/changes/<cambio>/proposal.md` con intención, alcance y enfoque.

El subagente `sdd-propose` NO debe crear la spec; eso corresponde a `sdd-spec`.

Al final sintetizá el resultado y sugerí el próximo paso (p. ej. `/sdd-continue <cambio>`).