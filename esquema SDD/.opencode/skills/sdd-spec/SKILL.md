---
name: sdd-spec
description: >
  Escribe especificaciones con requerimientos y escenarios (delta de especificaciones para el caso de cambios).
  Trigger: Cuando el orquestador te invoca para escribir o actualizar la especificación de un cambio.
license: MIT
metadata:
  author: AGCC took from gentleman-programming
  version: "2.0"
---

## Propósito

Eres un sub-agente responsable de escribir ESPECIFICACIONES. Tomas la propuesta y produces especificaciones delta — requisitos estructurados y escenarios que describen lo que se AGREGA, MODIFICA o ELIMINA del comportamiento del sistema.

## Qué recibes

Desde el orquestador:
- Nombre del cambio (Change name)
- Modo de almacenamiento de los artefactos (`openspec | none`)

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

### Paso 2: Identifica los Dominios Afectados

A partir de las "Áreas afectadas" ("Affected Areas") de la propuesta, determina qué dominios de especificación se ven afectados. Agrupa los cambios por dominio (p. ej., `auth/`, `payments/`, `ui/`).

### PAso 3: Lee las Especificaciones Existentes

**Si el modo es `openspec`:** si existe `openspec/specs/{domain}/spec.md`, léelo para entender el comportamiento ACTUAL. Tu especificación de delta describe CAMBIOS de este comportamiento.

**Si el modo es `none`:** Salta — no existen especificaciones para leer.

### PAso 4: Escribe Especificaciones Delta

**Si el modo es `openspec`:** Crea las especificaciones dentro del directorio de change:

```
openspec/changes/{change-name}/
├── proposal.md              ← (pre-existente)
└── specs/
    └── {domain}/
        └── spec.md          ← Delta spec
```

**Si el modo es `none`:** NO crear ningun directorio o archivo `openspec/`. Compone el contenido de la especificación en memoria — La persistirás en el paso 5.

#### Formato de Especificaciones Delta

```markdown
# Delta para {Domain}

## ADDED Requerimientos

### Requerimiento: {Requirement Name}

{Descripción usando palabras clave de RFC 2119: MUST, SHALL, SHOULD, MAY}

The system {MUST/SHALL/SHOULD} {do something specific}.

#### Escenario: {Happy path scenario}

- GIVEN {precondición}
- WHEN {acción}
- THEN {resultado esperado}
- AND {resultado adicional, si existe alguno}

#### Escenario: {Edge case scenario}

- GIVEN {precondición}
- WHEN {acción}
- THEN {resultado esperado}

## Requerimientos MODIFICADOS

### Requerimiento: {Existing Requirement Name}

{Nueva descripción — reemplaza la actualmente existente}
(Previamente: {lo que había antes})

#### Escenario: {Updated scenario}

- GIVEN {updated precondition}
- WHEN {updated action}
- THEN {updated outcome}

## Requerimientos ELIMINADOS

### Requerimiento: {Requerimiento que está siendo eliminado}

(Razón: {por qué este requerimiento está siendo deprecado/eliminado})
```

#### Para NUEVAS Especificaciones (No Existing Spec)

Si es un dominio completamente nuevo, crear una especificación FULL (no un delta):

```markdown
# {Dominio} Especificación

## Proósito

{Descripción de alto nivel del dominio de esta especificación.}

## Requerimientos

### Requerimiento: {Name}

El sistema {MUST/SHALL/SHOULD} {comportamiento}.

#### Escenario: {Name}

- GIVEN {precondición}
- WHEN {acción}
- THEN {resultado}
```

### Paso 5: Persistencia de Artefactos

**Este paso es MANDATORIO — NO lo saltees.**

Si el modo es `openspec`: el archivo ya fue escrito en el Paso 4.

Si salteas este paso, la próxima fase (sdd-tasks) NO podrá encontrar tu especificación y el pipeline SE ROMPE.

### Paso 6: Resumen de la Devolución

Devuelve al orquestador:

```markdown
## Especs Creadas

**Cambio**: {change-name}

### Especs Escritas
| Domain | Type | Requirements | Scenarios |
|--------|------|-------------|-----------|
| {domain} | Delta/New | {N added, M modified, K removed} | {total scenarios} |

### Cobertura
- Happy paths: {covered/missing}
- Edge cases: {covered/missing}
- Error states: {covered/missing}

### Próximo Paso
Listo para el diseño (sdd-design). Si el diseño ya existe, listo para las tareas (sdd-tasks).
```

## Reglas

- SIEMPRE usa el formato Given/When/Then para los escenarios
- SIEMPRE usa las palabras clave RFC 2119 (MUST, SHALL, SHOULD, MAY) para enfatizar el requerimiento
- Si ya hay especs existentes, escribe las specs DELTA (secciones ADDED/MODIFIED/REMOVED)
- Si NO hay especs existentes para el dominio, escribe una FULL espec
- Cada requerimiento DEBE tener al menos UN escenario
- Incluye ambos: escenarios happy path AND edge case
- Mantén los escenarios TESTABLES — Alguien debería poder escribir una prueba automatizada a partir de cada uno
- NO incluyas detalles de la implementación en las especs — la espec describe QUÉ, no CÓMO
- Aplica cada `rules.specs` de `openspec/config.yaml`
- Devuelve una estructura con: `status`, `executive_summary`, `detailed_report` (opcional), `artifacts`, `next_recommended`, y `risks`

## RFC 2119 Keywords Quick Reference

| Keyword | Meaning |
|---------|---------|
| **MUST / SHALL** | Absolute requirement |
| **MUST NOT / SHALL NOT** | Absolute prohibition |
| **SHOULD** | Recommended, but exceptions may exist with justification |
| **SHOULD NOT** | Not recommended, but may be acceptable with justification |
| **MAY** | Optional |
