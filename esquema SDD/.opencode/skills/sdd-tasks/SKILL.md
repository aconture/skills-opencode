---
name: sdd-tasks
description: >
  Desglosa un cambio en una lista de tareas de implementación.
  Trigger: cuando el orquestador te inicia para crear o actualizar el desglose de tareas de un cambio.
license: MIT
metadata:
  author: AGCC took from gentleman-programming
  version: "2.0"
---

## Propósito

Eres un subagente responsable de crear el DESGLOSE DE TAREAS. Tomas la propuesta, las especificaciones y el diseño, y produces un archivo `tasks.md` con pasos de implementación concretos y accionables, organizados por fase.

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

### Paso 2: Analiza el Diseño

A partir del documento de diseño, identifica:
- Todos los archivos que deben ser creados, modificados o eliminados
- El orden de dependencias (qué debe hacerse primero)
- Los requisitos de pruebas por componente

### Paso 3: Escribe tasks.md

**Si el modo es `openspec`:** Crea el archivo de tareas:

```
openspec/changes/{nombre-del-cambio}/
├── proposal.md
├── specs/
├── design.md
└── tasks.md               ← Tu creas este
```

**Si el modo es `none`:** NO crear ningun directorio `openspec/`. Compone el contenido de las tareas en memoria - las persistirás en el Paso 4.


#### Formato del Archivo de Tareas

```markdown
# Tareas: {Título del Cambio}

## Fase 1: {Nombre de la Fase} (e.g., Infraestructura / Fundamentos)

- [ ] 1.1 {Acción concreta — qué archivo, qué cambio}
- [ ] 1.2 {Acción concreta}
- [ ] 1.3 {Acción concreta}

## Fase 2: {Nombre de la Fase} (e.g., Implementación del Core)

- [ ] 2.1 {Acción concreta}
- [ ] 2.2 {Acción concreta}
- [ ] 2.3 {Acción concreta}
- [ ] 2.4 {Acción concreta}

## Fase 3: {Nombre de la Fase} (e.g., Testing / Verificación)

- [ ] 3.1 {Escribe los tests para ...}
- [ ] 3.2 {Escribe los tests para ...}
- [ ] 3.3 {Verifica la integración entre ...}

## Fase 4: {Nombre de la Fase} (e.g., Depuración / Documentación)

- [ ] 4.1 {Actualiza docs/commentarios}
- [ ] 4.2 {Elimina código temporario}
```

### Reglas de Escritura de Tareas

Cada tarea DEBE ser:

| Criterio | Ejemplo ✅ | Anti-ejemplo ❌ |
|----------|-----------|----------------|
| **Específica** | "Crea `internal/auth/middleware.go` con validación JWT" | "Add auth" |
| **Accionable** | "Agrega método `ValidateToken()` a `AuthService`" | "Handle tokens" |
| **Verificable** | "Test: `POST /login` devuelve 401 sin token" | "Make sure it works" |
| **Reducida** | Un archivo o unidad lógica por trabajo | "Implement the feature" |

### Lineamientos de la Organización de Fases

```
Fase 1: Fundamentos / Infraestructura
  └─ Nuevos tipos, interfaces, cambios de esquema de base de datos y configuración
  └─ Elementos requeridos por tareas posteriores

Fase 2: Implementación principal
  └─ Lógica central, reglas de negocio y comportamiento base
  └─ Parte principal del cambio

Fase 3: Integración / Conexión
  └─ Integrar componentes, rutas y cableado de la UI
  └─ Hacer que todo funcione en conjunto

Phase 4: Testing
  └─ Pruebas unitarias, pruebas de integración y pruebas end‑to‑end (E2E)
  └─ Verificar contra los escenarios definidos en la especificación

Fase 5: Limpieza (si es necesaria)
  └─ Documentación, eliminación de código muerto y pulido final
```

### Paso 4: Persistencia de Artefactos

**Este paso es MANDATORIO — NO lo saltees.**

Si el modo es `openspec`: el archivo ya fue escrito en el Paso 3.

Si salteas este paso, la próxima fase (sdd-apply) NO podrá encontrar tus tareas y el pipeline SE ROMPE.

### Paso 5: Resumen de la Devolución

Devuelve al orquestador:

```markdown
## Tareas Creadas

**Cambio**: {nombre-del-cambio}
**Ubicación**: `openspec/changes/{change-name}/tasks.md` (openspec) | inline (none)

### Desglose de Tareas
| Phase | Tasks | Focus |
|-------|-------|-------|
| Phase 1 | {N} | {Phase name} |
| Phase 2 | {N} | {Phase name} |
| Phase 3 | {N} | {Phase name} |
| Total | {N} | |

### Orden de Implementación
{Descripción breve del orden recomendado y el motivo}

### Próximo Paso
Listo para la implementación (sdd-apply).
```

## Reglas

- SIEMPRE referencia rutas de archivos concretas en las tareas
- Las tareas DEBEN estar ordenadas por dependencias — las tareas de la Fase 1 no deben depender de la Fase 2
- Las tareas de testing deben hacer referencia a escenarios específicos definidos en las especificaciones
- Cada tarea debe poder completarse en UNA sola sesión (si una tarea resulta demasiado grande, divídela)
- Usa numeración jerárquica: 1.1, 1.2, 2.1, 2.2, etc
- NUNCA incluyas tareas vagas como “implementar funcionalidad” o “agregar pruebas”
- Aplica cualquier regla `rules.tasks` definida en `openspec/config.yaml`
- Si el proyecto utiliza TDD, integra tareas con enfoque test‑first: tarea RED (escribir la prueba que falla) → tarea GREEN (hacer que pase) → tarea REFACTOR (limpiar y mejorar el código).
- Devuelve un contenedor estructurado con: `status`, `executive_summary`, `detailed_report` (optional), `artifacts`, `next_recommended`, and `risks`
