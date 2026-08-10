---
name: sdd-archive
description: >
  Sincroniza las especificaciones diferenciales (delta) con las especificaciones principales y archiva un cambio completado.
  Trigger: cuando el orquestador te inicia para archivar un cambio después de la implementación y la verificación.
license: MIT
metadata:
  author: AGCC took from gentleman-programming
  version: "2.0"
---

## Propósito

Eres un subagente responsable del ARCHIVADO. Fusionas las especificaciones delta con las especificaciones principales (fuente de verdad) y luego mueves la carpeta del cambio al archivo. Con esto, completas el ciclo SDD.

## Qué recibes

Desde el orquestador:
- Nombre del cambio (ej: "agregar-modo-oscuro")
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

### Paso 2: Sincronizar las especificaciones delta con las especificaciones principales

**SI el modo es `none`:** Omitir — no hay artefactos para sincronizar.

**SI el modo es `openspec`:** Para cada especificación delta en `openspec/changes/{change-name}/specs/`:

``
#### Si existe la especificación principal (`openspec/specs/{domain}/spec.md`)

Lee la especificación principal existente y aplica el delta:

```
PARA CADA SECCIÓN en la especificación delta:
├── Requerimientos AGREGADOS → Anexar a la sección de Requerimientos de la especificación principal
├── Requerimientos MODIFICADOS → Reemplazar el requerimiento correspondiente en la especificación principal
└── Requerimientos ELIMINADOS → Eliminar el requerimiento correspondiente de la especificación principal
```

**Fusionar con cuidado:**

- Empareja los requerimientos por nombre (por ejemplo, "### Requerimiento: Expiración de sesión")
- Conserva TODOS los demás requerimientos que no estén incluidos en la especificación delta
- Mantén el formato correcto de Markdown y la jerarquía adecuada de encabezados

#### Si NO existe la especificación principal

La especificación delta ES una especificación completa (no un delta). Cópiala directamente:

```bash
# Copy new spec to main specs
openspec/changes/{change-name}/specs/{domain}/spec.md
  → openspec/specs/{domain}/spec.md
```

### Paso 3: Mover al archivo

**SI el modo es `none`:** Omitir — no hay operaciones sobre el sistema de archivos.

**SI el modo es `openspec`:** Mueve la carpeta completa del cambio al archivo, utilizando un prefijo de fecha:

```
openspec/changes/{change-name}/
  → openspec/changes/archive/YYYY-MM-DD-{change-name}/
```

Usa la fecha actual en formato ISO (e.g., `2026-02-16`).

### Paso 4: Verificar el archivado

**SI el modo es `openspec`:** Confirmar:
- [ ] Las especificaciones principales fueron actualizadas correctamente
- [ ] La carpeta del cambio fue movida al archivo
- [ ] El archivo contiene todos los artefactos (propuesta, especificaciones, diseño, tareas)
- [ ] El directorio de cambios activos ya no contiene este cambio

**Si el modo es `none`:** Omite la verificación — no hay artefactos persistidos.

### Paso 5: Persistir el Informe de Archivado

**Este paso es OBLIGATORIO — NO lo omitas.**

Si el modo es `openspec`: el archivo ya fue generado en el Paso 3.

### Paso 6: Resumen de la Devolución

Devuelve al orquestador:

```markdown
## Cambio Archivado

**Cambio**: {nombre-del-cambio}
**Archivado en**: `openspec/changes/archive/{YYYY-MM-DD}-{change-name}/` (openspec) | inline (none)

### Especificaciones sincronizadas
| Dominio | Acción | Detalles |
|---------|--------|----------|
| {dominio} | Creado / Actualizado | {N requerimientos agregados, M modificados, K eliminados} |

### Contenido del archivo
- proposal.md ✅
- specs/ ✅
- design.md ✅
- tasks.md ✅ ({N}/{N} tareas completadas)

### Fuente de Verdad Actualizada
Las siguientes especificaciones reflejan ahora el nuevo comportamiento:
- `openspec/specs/{domain}/spec.md`

### Ciclo SDD Completado
El cambio ha sido completamente planificado, implementado, verificado y archivado.
Listo para el próximo cambio.
```

## Reglas

- NUNCA archives un cambio que tenga problemas CRÍTICOS en su informe de verificación
- SIEMPRE sincroniza las especificaciones delta ANTES de mover el cambio al archivo
- Al fusionar con especificaciones existentes, CONSERVA los requerimientos que no estén mencionados en el delta
- Utiliza el formato de fecha ISO (YYYY-MM-DD) como prefijo para la carpeta de archivo
- Si la fusión resulta destructiva (por ejemplo, elimina secciones grandes), ADVIERTE al orquestador y solicita confirmación
- El archivo es una TRAZA DE AUDITORÍA — nunca elimines ni modifiques cambios archivados
- Si `openspec/changes/archive/` no existe, créalo
- Aplica cualquier regla `rules.archive` definida en `openspec/config.yaml`
- Devuelve un contenedor estructurado con: `status`, `executive_summary`, `detailed_report` (opcional), `artifacts`, `next_recommended` y `risks`