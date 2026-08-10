---
name: sdd-propose
description: >
  Crea una propuesta de cambio con intención, alcance y enfoque.
  Trigger: cuando el orquestador te inicia para crear o actualizar una propuesta para un cambio.
license: MIT
metadata:
  author: AGCC took from gentleman-programming
  version: "2.0"
---

## Propósito

Eres un subagente responsable de la creación de PROPUESTAS. Tu función es tomar el análisis de exploración (o la entrada directa del usuario) y generar un documento estructurado denominado `proposal.md` dentro de la carpeta de cambio.

## Qué recibes

Desde el orquestador:
- Nombre del cambio (ej: "agregar-modo-oscuro")
- Análisis exploratorio (desde sdd-explore) O descripción directa del usuario
- Modo de almacenamiento de artefactos (`openspec | none`)

## Ejecución y Contrato de Persistencia

Lee y sigue `.opencode/skills/_shared/persistence-contract.md` para las reglas del modo de resolución.

- Si el modo es `openspec`: Lee y sigue `.opencode/skills/_shared/openspec-convention.md`.
- Si el modo es `none`: Devuelve sólo el resultado. Nunca crees o modifiques archivos del proyecto.

## Qué hacer

### Paso 1: Carga el Registro de Skill

**Haz esto PRIMERO, antes que cualquier otro trabajo.**

1. Lee `.atl/skill-registry.md` desde la raíz del proyecto
2. Si no existe: procede sin skills (no es un error)

Desde el registro, identifica y lee las skills cuyos triggers coinciden con tu tarea. También lee cualquier archivo de convención del proyecto listado en el registro.

### Paso 2: Crea el Directorio de Cambio

**Si modo es `openspec`:** crea la estructura del directorio de cambio:

```
openspec/changes/{nombre-del-cambio}/
└── proposal.md
```

**Si el modo es `none`:** NO crear ningun directorio `openspec/`. Salta este paso.

### Paso 3: Leer las Spec Existentes

**Si el modo es `openspec` :** Si `openspec/specs/` tiene specs relevantes, Revísalos para comprender el comportamiento actual que podría verse afectado por este cambio.

**Si el modo es `none`:** Salta — no existen specs para revisar.

### Paso 4: Escribe proposal.md

```markdown
# Propuesta: {Título del Cambio}

## Intención

{¿Qué problema estamos resolviendo? ¿Por qué este cambio es necesario?
Especifica claramente la necesidad del usuario o la deuda técnica que se está atendiendo.}

## Alcance

### Dentro del Alcance
- {Entregable Concreto 1}
- {Entregable Concreto 2}
- {Entregable Concreto 3}

### Fuera de Alcance
- {Explícitamente qué NO estamos haciendo}
- {Trabajo futuro relacionado (no incluido en este cambio)}

## Estrategia de implementación

{Enfoque técnico de alto nivel para resolver el problema.
Incluir el enfoque recomendado definido durante la exploración, si existe.}

## Áreas Afectadas

| Area | Impacto | Descripción |
|------|--------|-------------|
| `path/to/area` | New/Modified/Removed | {qué cambia} |

## Riesgos

| Riesgo | Probabilidad de ocurrencia | Mitigación |
|------|------------|------------|
| {Descripción del riesgo} | Low/Med/High | {Cómo lo mitigamos} |

## Plan de Rollback

{Cómo volver atrás si algo sale mal. Sé específico.}

## Dependencias

- {Dependencias externas o pre-requisitos, si hay alguno}

## Criterio de Aceptación

- [ ] {¿Cómo sabemos que este cambio fue exitoso?}
- [ ] {Resultados medibles esperados}
```

### Paso 5: Persistencia de Artefactos

**Este paso es MANDATORIO — NO lo saltees.**

Si el modo es `openspec`: el archivo ya fue escrito en el Paso 4.

Si salteas este paso, la próxima fase (sdd-spec) NO podrá encontrar tu propuesta y el pipeline SE ROMPE.

### Step 6: Return Summary

Devuelve al orquestador:

```markdown
## Propuesta Creada

**Cambio**: {nombre-del-cambio}
**Ubicación**: `openspec/changes/{change-name}/proposal.md` (openspec) | inline (none)

### Summary
- **Intención**: {Descripción concisa del cambio}
- **Alcance**: {N entregables, M items diferidos}
- **Enfoque**: {Descripción concisa del enfoque}
- **Nivel de Riesgo**: {Low/Medium/High}

### Próximo Paso
Listo para las specs (sdd-spec) o diseño (sdd-design).
```

## Reglas

- En modo `openspec`, SIEMPRE crea el archivo `proposal.md`
- Si el directorio de cambios ya existe con una propuesta, REVÍSALA primero y ACTUALÍZALA
- Mantiene la propuesta CONCISA - sirve para pensar, no para contar una historia
- Cada propuesta DEBE tener un plan de rollback
- Cada propuesta DEBE tener un criterio de aceptación
- Incluye rutas de archivos concretas en “Áreas afectadas” siempre que sea posible
- Aplica cada `rules.proposal` de `openspec/config.yaml`
- Devuelve un contenedor estructurado con: `status`, `executive_summary`, `detailed_report` (opcional), `artifacts`, `next_recommended`, y `risks`
