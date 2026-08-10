# Registro de la Skill

Como PRIMER paso antes de comenzar cualquier trabajo, identifica y carga las skills relevantes para tu tarea desde este registro.

## Skills SDD (Orquestación)

Skills del flujo Spec-Driven Development, usadas por el orquestador y los subagentes. Rutas relativas a la raíz del proyecto.

| Trigger | Skill | Ruta |
|---------|-------|------|
| inicializar SDD, "sdd init", "iniciar sdd", "openspec init" | `sdd-init` | `.opencode/skills/sdd-init/SKILL.md` |
| asignación del orquestador: pensar una funcionalidad, investigar el codebase, aclarar requisitos | `sdd-explore` | `.opencode/skills/sdd-explore/SKILL.md` |
| asignación del orquestador: crear o actualizar una propuesta para un cambio | `sdd-propose` | `.opencode/skills/sdd-propose/SKILL.md` |
| asignación del orquestador: escribir o actualizar la especificación de un cambio | `sdd-spec` | `.opencode/skills/sdd-spec/SKILL.md` |
| asignación del orquestador: escribir o actualizar el diseño técnico para un cambio | `sdd-design` | `.opencode/skills/sdd-design/SKILL.md` |
| asignación del orquestador: crear o actualizar el desglose de tareas de un cambio | `sdd-tasks` | `.opencode/skills/sdd-tasks/SKILL.md` |
| asignación del orquestador: implementar una o más tareas de un cambio | `sdd-apply` | `.opencode/skills/sdd-apply/SKILL.md` |
| asignación del orquestador: verificar un cambio completado (o parcialmente completado) | `sdd-verify` | `.opencode/skills/sdd-verify/SKILL.md` |
| asignación del orquestador: archivar un cambio tras implementación y verificación | `sdd-archive` | `.opencode/skills/sdd-archive/SKILL.md` |
| "update skills", "skill registry", "actualizar skills", "update registry" | `skill-registry` | `.opencode/skills/skill-registry/SKILL.md` |

## Skills del usuario

| Trigger | Skill | Ruta |
|---------|-------|------|
| (ninguna skill de codificación/tarea de usuario definida en este proyecto) | — | — |

## Convenciones del proyecto

| Archivo | Ruta | Notas |
|---------|------|-------|
| `AGENTS.md` | `AGENTS.md` | Índice — incluye reglas de portabilidad y referencia a las reglas y al orquestador |
| `.opencode/rules/rules.md` | `.opencode/rules/rules.md` | Reglas de código (portabilidad, clean code, idioma) |
| `.opencode/rules/sdd-orchestrator.md` | `.opencode/rules/sdd-orchestrator.md` | Orquestador SDD: reglas de delegación, comandos y gráfico de dependencias |
| `.opencode/opencode.json` | `.opencode/opencode.json` | Config de opencode: agente `orchestrator` e instrucciones |
| `skills/_shared/persistence-contract.md` | `.opencode/skills/_shared/persistence-contract.md` | Contrato de persistencia de artefactos SDD |
| `skills/_shared/openspec-convention.md` | `.opencode/skills/_shared/openspec-convention.md` | Convención de disposición de archivos en modo openspec |

Lee los archivos de convenciones listados arriba para conocer los patrones y reglas específicos del proyecto.