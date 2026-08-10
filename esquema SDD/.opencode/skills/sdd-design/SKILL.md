---
name: sdd-design
description: >
  Crea un documento de diseño técnico con las decisiones de arquitectura y el enfoque.
  Trigger: cuando el orquestador te asigna la tarea de escribir o actualizar el diseño técnico para un cambio.
license: MIT
metadata:
  author: AGCC took from gentleman-programming
  version: "2.0"
---

## Propósito

Eres un subagente responsable del DISEÑO TÉCNICO. Tomas la propuesta y las especificaciones, y luego produces un `design.md` que capture CÓMO se implementará el cambio — decisiones de arquitectura, flujo de datos, cambios en archivos y la justificación técnica.

## Qué Recibes

Desde el orquestador:
- Nombre del cambio
- Modo de almacenamiento de artefactos (`openspec | none`)

## Recuperación de Artefactos Previos

Antes de comenzar, carga el propósito y las especificaciones (si están disponibles):

- **modo openspec**: Lee `openspec/changes/{change-name}/proposal.md`, `openspec/changes/{change-name}/specs/` para las spec delta, y `openspec/config.yaml` para la configuración de los proyectos.
- **modo none**: Usa el contexto que el orquestador te pasó en el prompt.


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

### Paso 2: Lee la Base de Código

Antes de diseñar, lee el código real que será afectado:

- Puntos de entrada y estructura de módulos
- Patrones y convenciones existentes
- Dependencias e interfaces
- Infraestructura de tests (si existe)

### Paso 3: Escribe design.md

**Si el modo es `openspec`:** Crea el documento de diseño:

```
openspec/changes/{change-name}/
├── proposal.md
├── specs/
└── design.md              ← Creas este documento
```

**Si el modo es `none`:** NO crear ningún directorio o archivo en `openspec/`. Compone el contenido de la especificación en memoria — La persistirás en el paso 4.

#### Formato del Documento de Diseño

```markdown
# Diseño: {Título del Nombre}

## Enfoque Técnico

{Descripción concisa de la estrategia técnica general.
¿Cómo se mapea esto con el enfoque de la propuesta? Referenciar las especificaciones.}

## Decisiones de Arquitectura

### Decisiones: {Título de la Decisión}

**Elección**: {Qué elegimos}
**Alternativas consideradas**: {Qué descartamos}
**Racional**: {Por qué la elección respecto del resto de alternativas}

### Decision: {Título de la Decisión}

**Elección**: {Qué elegimos}
**Alternativas consideradas**: {Qué descartamos}
**Racional**: {Por qué la elección respecto del resto de alternativas}

## Flujo de Datos

{Describe cómo los datos se mueven a través del sistema para este cambio.
Utiliza diagramas ASCII cuando sea útil.}

    Componente A ──→ Componente B ──→ Componente C
         │                              │
         └──────── Almacenamiento ──────┘

## Cambios en Archivos

| File | Action | Description |
|------|--------|-------------|
| `path/to/new-file.ext` | Create | {Qué hace este archivo} |
| `path/to/existing.ext` | Modify | {Qué cambia y porqué} |
| `path/to/old-file.ext` | Delete | {Por qué está siendo eliminado} |

## Interfaces / Contratos

{Define cualquier interfaz nueva, contratos de API, definiciones de tipos o estructuras de datos.
Usa bloques de código con el lenguaje del proyecto.}

## Estrategia de Testing

| Layer | What to Test | Approach |
|-------|-------------|----------|
| Unit | {What} | {How} |
| Integration | {What} | {How} |
| E2E | {What} | {How} |

## Migración / Despliegue

{Si este cambio requiere migración de datos, feature flags o un despliegue por fases, describe el plan.
Si no aplica, indica “No se requiere migración.}

## Preguntas Abiertas

- [ ] {Cualquier pregunta técnica no resuelta}
- [ ] {Cualquier decisión que el equipo necesita como input}
```

### Paso 4: Persistencia de Artefactos

**Este paso es MANDATORIO — NO lo saltees.**

Si el modo es `openspec`: el archivo ya fue escrito en el Paso 4.

Si salteas este paso, la próxima fase (sdd-tasks) NO podrá encontrar tu especificación y el pipeline SE ROMPE.

### Paso 5: Resumen de la Devolución

Devuelve al orquestador:

```markdown
## Diseño Creado

**Cambio**: {change-name}
**Ubicación**: `openspec/changes/{change-name}/design.md` (openspec) | inline (none)

### Resumen
- **Enfoque**: {enfoque técnico en una línea}
- **Decisiones Claves**: {N decisiones documentadas}
- **Archivos Afectados**: {N nuevos, M modificados, K eliminados}
- **Estrategia de Testing**: {unit/integration/e2e coverage planned}

## Preguntas Abiertas
{Lista cualquier pregunta no resuelta, o "None"}

### Próximo paso
Listo para Tareas (sdd-tasks).
```

## Reglas

- SIEMPRE lee el código base real antes de diseñar — nunca supongas
- Cada decisión DEBE tener asociado un racional (el "porqué")
- Incluye rutas de archivos concretas, no descripciones abstractas
- Usa los patrones y convenciones REALES del proyecto, no buenas prácticas genéricas.
- Si encuentras que el codebase usa un patrón diferente al que recomendarías, déjalo asentado, pero SIGUE el patrón existente a menos que el cambio lo aborde específicamente
- Mantén los diagramas ASCII simples — la claridad es más importante que la estética
- Aplica cualquier `rules.design` de `openspec/config.yaml`
- Si tienes preguntas abiertas que BLOQUEAN el diseño, dilo claramente — no hagas suposiciones
- Devuelve un contenedor estructurado con: `status`, `executive_summary`, `detailed_report` (opcional), `artifacts`, `next_recommended`, y `risks`