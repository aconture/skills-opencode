# OpenSpec File Convention (shared across all SDD skills)

## Directory Structure

```
openspec/
├── config.yaml              <- Project-specific SDD config
├── specs/                   <- Source of truth (main specs)
│   └── {domain}/
│       └── spec.md
└── changes/                 <- Active changes
    ├── archive/             <- Completed changes (YYYY-MM-DD-{change-name}/)
    └── {change-name}/       <- Active change folder
        ├── state.yaml       <- DAG state (survives compaction)
        ├── exploration.md   <- (optional) from sdd-explore
        ├── proposal.md      <- from sdd-propose
        ├── specs/           <- from sdd-spec
        │   └── {domain}/
        │       └── spec.md  <- Delta spec
        ├── design.md        <- from sdd-design
        ├── tasks.md         <- from sdd-tasks (updated by sdd-apply)
        └── verify-report.md <- from sdd-verify
```

## Artifact File Paths

| Skill | Creates / Reads | Path |
|-------|----------------|------|
| orchestrator | Creates/Updates | `openspec/changes/{change-name}/state.yaml` |
| sdd-init | Creates | `openspec/config.yaml`, `openspec/specs/`, `openspec/changes/`, `openspec/changes/archive/` |
| sdd-explore | Creates (optional) | `openspec/changes/{change-name}/exploration.md` |
| sdd-propose | Creates | `openspec/changes/{change-name}/proposal.md` |
| sdd-spec | Creates | `openspec/changes/{change-name}/specs/{domain}/spec.md` |
| sdd-design | Creates | `openspec/changes/{change-name}/design.md` |
| sdd-tasks | Creates | `openspec/changes/{change-name}/tasks.md` |
| sdd-apply | Updates | `openspec/changes/{change-name}/tasks.md` (marks `[x]`) |
| sdd-verify | Creates | `openspec/changes/{change-name}/verify-report.md` |
| sdd-archive | Moves | `openspec/changes/{change-name}/` → `openspec/changes/archive/YYYY-MM-DD-{change-name}/` |
| sdd-archive | Updates | `openspec/specs/{domain}/spec.md` (merges deltas into main specs) |

## Reading Artifacts

```
Proposal:  openspec/changes/{change-name}/proposal.md
Specs:     openspec/changes/{change-name}/specs/  (all domain subdirectories)
Design:    openspec/changes/{change-name}/design.md
Tasks:     openspec/changes/{change-name}/tasks.md
Verify:    openspec/changes/{change-name}/verify-report.md
Config:    openspec/config.yaml
Main specs: openspec/specs/{domain}/spec.md
```

## Reglas de Escritura

- SIEMPRE crea el directorio de cambios antes de generar los artefactos
- Si un archivo ya existe, LEERLO antes y luego ACTUALIZARLO (no lo sobreescribas a ciegas)
- Si el directorio de cambios ya existe y contiene artefactos, el cambio es una CONTINUACION
- Usa la sección `openspec/config.yaml` `rules` para aplicar constraints específicas del proyecto para cada fase

## Config File Reference

```yaml
# openspec/config.yaml
schema: spec-driven

context: |
  Tech stack: {detected}
  Architecture: {detected}
  Testing: {detected}
  Style: {detected}

rules:
  proposal:
    - Incluye 'rollback plan' para cambios riesgosos
  specs:
    - Usa Dado/Cuando/Entonces para los escenarios
    - Usa RFC 2119 keywords (MUST, SHALL, SHOULD, MAY)
  design:
    - Incluye diagramas de secuencia para flujos complejos
    - Documenta decisiones de arquitectura con su racional
  tasks:
    - Agrupa por fase, usa numeración jerárquica
    - Mantiene las tareas completables en una sesión
  apply:
    - Sigue patrones de código existente
    tdd: false           # Set to true to enable RED-GREEN-REFACTOR
    test_command: ""
  verify:
    test_command: ""
    build_command: ""
    coverage_threshold: 0
  archive:
    - Notifica antes de ejecutar merge de deltas destructivos
```

## Archive Structure

Cuando archivas, mueve el directorio de cambio a:
```
openspec/changes/archive/YYYY-MM-DD-{change-name}/
```

Usa la fecha de hoy en formato ISO. 
Use today's date in ISO format. El archivo es una PISTA DE AUDITORÍA - es inmutable, nunca borres o modifiques cambios archivados.
