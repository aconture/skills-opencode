---
name: sdd-init
description: >
  Inicializa el contexto Spec-Driven Development (SDD) en cualquier proyecto. Detecta el stack técnico, las convenciones y arranca el backend de persistencia activo.
  Trigger: Cuando el usuario quiera inicializar SDD en un proyecto, o diga "sdd init", "iniciar sdd", "openspec init".
license: MIT
metadata:
  author: AGCC took from gentleman-programming
  version: "2.0"
---

## Propósito

Eres un subagente responsable de inicializar el contexto de Spec-Driven Development (SDD) en un proyecto. Debe detectar el stack tecnológico y las convenciones del proyecto, y luego arrancar el backend de persistencia activo.

## Contrato de Ejecución y Persistencia

Lee y sigue `.opencode/skills/_shared/persistence-contract.md` para aplicar el modo y las reglas de resolución.

- Si el modo es `openspec`: Lee y sigue `.opencode/skills/_shared/openspec-convention.md`. Ejecuta un arranque full.
- Si el modo es `none`: Devuelve el contexto detectado sin escribir archivos del proyecto.

## Qué hacer

### Paso 1: Detecta el Contexto del Proyecto

Lee el proyecto para entender:
- Stack tecnológico (check package.json, go.mod, pyproject.toml, etc.)
- Convenciones existentes (linters, test frameworks, CI)
- Patrones de arquitectura en uso

### Paso 2: Inicializa el Backend de Persistencia

Si el modo es `openspec`, crea esta estructura de directorios:

```
openspec/
├── config.yaml              ← Configuración SDD específica del proyecto
├── specs/                   ← Fuente de verdad (vacío inicialmente)
└── changes/                 ← Cambios activos
    └── archive/             ← Cambios completados
```

### Paso 3: Genera Config (openspec mode)

Basado en lo que has detectado, la configuración en modo `openspec`:

```yaml
# openspec/config.yaml
schema: spec-driven

contexto: |
  Stack Tecnológico: {detected stack}
  Arquitectura: {detected patterns}
  Testing: {detected test framework}
  Estilo: {detected linting/formatting}

reglas:
  proposal:
    - Incluye un plan de rollback para los cambios riesgosos
    - Identifica los módulos/packages afectados
  specs:
    - Usa el formato Dado/Cuando/Entonces para los escenarios
    - Usa las palabras clave de RFC 2119 (MUST, SHALL, SHOULD, MAY)
  design:
    - Incluye diagramas de secuencia para flujos complejos
    - Documenta decisiones de arquitectura con su racional
  tasks:
    - Agrupa las tareas por fase (infraestructura, implementación, testing)
    - Usa numeración jerárquica (1.1, 1.2, etc.)
    - Mantiene las tareas lo suficientemente pequeñas como para completarlas en una sesión
  apply:
    - Sigue patrones y convenciones del código existente
    - Carga las skills relevantes para el stack del proyecto
  verify:
    - Ejecuta tests si la infraestructura de test existe
    - Compara la implementación contra cada escenario de la espec
  archive:
    - Advierte antes de mergear deltas destructivos (eliminaciones largas)
```

### PAso 4: Construye el Registro de Skill

Sigue la misma lógica que la skill `skill-registry` (`skills/skill-registry/SKILL.md`):

1. DEBES escanear las skills de usuario CADA vez que se ejecuta /sdd-init: 
1. DEBES escanear las skills de usuario, CADA vez que se ejecuta /sdd-init: realiza un glob `*/SKILL.md` en TODOS los directorios de skills conocidos. **User-level**: `~/.gemini/skills/`, parent of this skill file. **Project-level**: `.opencode/skills/`, `.gemini/skills/`, `.agent/skills/`, `skills/`. Excluye `_shared` de la sección de usuario; las skills `sdd-*` (excepto `skill-registry`) van en la sección "Skills SDD (Orquestación)" del registro. Deduplicate by name (project-level wins). Read frontmatter triggers.
2. Escanea las convenciones del proyecto: chequea `agents.md`, `AGENTS.md`, (project-level), `GEMINI.md`, en el root del proyecto. Si encuentras un archivo índice (e.g., `agents.md`), LEELO y extrae todos los path referenciados — incluye ambos el índice y sus archivos referenciados en el registro.
3. **SIEMPRE escribe `.atl/skill-registry.md`** en el raiz del proyecto (crea `.atl/` si es necesario). Este archivo es independiente del modo — Es infraestructura, no un artefacto SDD.

Mira `skills/skill-registry/SKILL.md` para el formato de la full registración y los detalles del escaneo.

### Paso 5: Persiste el Contexto del Proyecto

**Este paso es MANDATORIO — NO lo saltees.**

Si modo es `openspec`: la configuración ya se escribió en el Paso 3.

### Paso 6: Resumen de la Respuesta

Devuelve un resumen estructurado adaptado al modo resuelto:

#### Si modo es `openspec`:
```
## SDD Initializado

**Proyecto**: {project name}
**Stack**: {detected stack}
**Persistencia**: openspec

### Estructura Creada
- openspec/config.yaml ← Configuración del proyecto con el contexto detectado
- openspec/specs/      ← Listo para las especificaciones
- openspec/changes/    ← Listo para las propuestas de cambio

### Próximos pasos
Listo para /sdd-explore <topic> o /sdd-new <nombre-del-cambio>.
```

#### Si modo es `none`:
```
## SDD Initializado

**Proyecto**: {project name}
**Stack**: {detected stack}
**Persistencia**: none (ephemeral)

### Contexto Detectado
{resumen del stack y las convenciones detectadas}

### Recommendaciones
Habilita `openspec` para la persistencia de artefactos a lo largo de las sesiones. Sin persistencia, todos los artefactos SDD se perderán cuando la conversación finaliza.

### Próximos Pasos
Listo para /sdd-explore <topic> o /sdd-new <nombre-del-cambio>. 
```

## Reglas

- NUNCA crees archivos de especificación temporales (placeholders) — las especificaciones se crean mediante sdd-spec durante un cambio
- SIEMPRE detecta el stack tecnológico real, no supongas
- Si el proyecto ya tiene un directorio `openspec/`, reporta su existencia y consulta al orquestador si debe ser actualizado
- Mantiene el contexto de config.yaml CONCISO - no más de 10 líneas
- Devuelve una envolvente estructurada con: `status`, `resumen_ejecutivo`, `reporte_detallado` (opcional), `artefactos`, `prox_recomendacion`, y `riesgos`
