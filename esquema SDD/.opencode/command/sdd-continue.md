---
description: Continúa el próximo artefacto faltante en la cadena SDD (uso: /sdd-continue [cambio])
---

Actuá como orquestador SDD. Leé y cumplí las reglas de orquestación en `.opencode/rules/sdd-orchestrator.md`.

Cambio (opcional): $ARGUMENTS

Determiná el próximo artefacto faltante en la cadena de dependencias:

```
proposal -> specs -> tasks -> apply -> verify -> archive
              ^
              |
            design
```

Luego ejecutá esa fase delegándola a un subagente con el tool `task`. En el prompt del subagente incluí esta sección obligatoria:

```
SKILL LOADING (do this FIRST):
  1. Revisá las habilidades disponibles: leé el registro `.atl/skill-registry.md` y, si tenés el tool `skill`, cargá las skills cuyo trigger coincida.
  2. Cargá y seguí la skill `sdd-<fase>` y, si no, leé directamente `.opencode/skills/sdd-<fase>/SKILL.md`.
```

Respetá el gráfico de dependencias: cada fase lee los artefactos requeridos desde `openspec/changes/<cambio>/` (o el directorio que use el backend) y escribe el suyo. Al final sintetizá el resultado: `status`, `executive_summary`, `artifacts`, `next_recommended`, `risks`.