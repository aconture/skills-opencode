---
name: sdd-apply
description: >
  Implementa las tareas del cambio, escribiendo código real de acuerdo con las especificaciones y el diseño.
  Trigger: cuando el orquestador te inicia para implementar una o más tareas de un cambio.

license: MIT
metadata:
  author: AGCC took from gentleman-programming
  version: "2.0"
---

## Propósito

Eres un subagente responsable de la IMPLEMENTACIÓN. Recibes tareas específicas desde `tasks.md` y las implementas escribiendo código real. Debes seguir estrictamente las especificaciones y el diseño.

## Qué Recibes

Desde el orquestador:
- Nombre del cambio
- La tarea(s) específica a implementar (e.g., "Fase 1, tareas 1.1-1.3")
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

### Paso 2: Lee el Contexto

Antes de escribir CUALQUIER código:
1. Lee las especificaciones - entiende QUÉ debe hacer el código
2. Lee el diseño - entiende CÓMO estructurar el código
3. Lee el código existente en los archivos afectados - entiende los patrones actuales
4. Verifica las convenciones de codificación del proyecto en `config.yaml`

### Paso 3: Detecta el Modo de Implementación

Antes de escribir código, determina si el proyecto usa TDD:

```
Detecta modo TDD desde (en orden prioritario):
├── openspec/config.yaml → rules.apply.tdd (true/false — highest priority)
├── Skills instaladas por el usuario (e.g., existe tdd/SKILL.md)
├── Patrones de test existentes en la base de código (Archivos de prueba junto al código fuente)
└── Default: modo standard (escribe código primero, luego verifica)

Si se detecta el modo TDD → usa Paso 3a (Workflow TDD)
Si modo standard → usa Paso 3b (Workflow Standard)
```

### Paso 3a: Tareas de Implementación (Workflow TDD — RED → GREEN → REFACTOR)

When TDD is active, EVERY task follows this cycle:

```
PARA CADA TAREA:
├── 1. ENTIENDE
│   ├── Lee la descripción de la tarea
│   ├── Lee los escenarios relevantes de la especificación (estos son tus criterios de aceptación)
│   ├── Lee las decisiones de diseño (Estos elementos restringen y definen tu enfoque)
│   └── Lee el código existente y los patrones de pruebas
│
├── 2. RED — Escribe primero una prueba que falle
│   ├── Escribe prueba(s) que describan el comportamiento esperado a partir de los escenarios de la especificación
│   ├── Correr tests → deben fallar
│   └── Si pasan de entrada, algo está mal
│
├── 3. GREEN — Escribe el código mínimo para que pase
│   ├── Implementa SOLO lo necesario para que las pruebas que fallan pasen
│   ├── Ejecuta las pruebas — confirma que PASAN
│   └── NO agregues funcionalidad extra más allá de lo que la prueba requiere
│
├── 4. REFACTOR — Limpieza sin cambiar el comportamiento
│   ├── Mejora la estructura del código, los nombres y elimina duplicación
│   ├── Ejecuta las pruebas nuevamente — confirma que SIGUEN PASANDO
│   └── Alinea el código con las convenciones y patrones del proyecto
│
├── 5. Marca la tarea como completada [x] en tasks.md
└── 6. Documenta cualquier incidencia o desviación
```

Identifica el framework de ejecución de pruebas utilizado

```
Identifica el framework de ejecución de pruebas desde:
├── openspec/config.yaml → rules.apply.test_command (la mayor prioridad)
├── package.json → scripts.test
├── pyproject.toml / pytest.ini → pytest
├── Makefile → make test
└── Fallback: informa que las pruebas no pudieron ejecutarse automáticamente
```

**Importante**: Si hay skills de coding instaladas por el usuario (por ejemplo, `tdd/SKILL.md`, `pytest/SKILL.md`, `vitest/SKILL.md`), léelas y sigue esos patrones para la escritura de pruebas.

### Paso 3b: Implementa las Tareas (Workflow Standard)

Cuando TDD no está activo:

```
PARA CADA TAREA:
├── Lee la descripción de la tarea
├── Lee los escenarios relevantes de la especificación (estos son tus criterios de aceptación)
├── Lee las decisiones de diseño (estas condicionan tu enfoque)
├── Revisa los patrones de código existentes (ajústate al estilo del proyecto)
├── Escribe el código
├── Marca la tarea como completada [x] en tasks.md
└── Documenta cualquier incidencia o desviación
```

### Paso 4: Marca las Tareas Completadas

Actualiza `tasks.md` — cambia `- [ ]` a `- [x]` para las tareas completadas:

```markdown

## Fase 1: Fundamentos

- [x] 1.1 Crear `internal/auth/middleware.go` con validación JWT
- [x] 1.2 Agregar la estructura `AuthConfig` en `internal/config/config.go`
- [ ] 1.3 Agregar las rutas de autenticación en `internal/server/server.go`  ← aún pendiente
``

### Paso 5: Persistir el Progreso

**Este paso es MANDATORIO — NO lo saltees.**

Si el modo es `openspec`: `tasks.md` ya fue actualizado en el Paso 4.

Si salteas este paso, la próxima fase (sdd-verify) NO podrá encontrar tu progreso y el pipeline SE ROMPE.

### Paso 5: Resumen de la Devolución

Devuelve al orquestador:

```markdown
## Progreso de la Implementación

**Cambio**: {nombre-del-cambio}
**Modo**: {TDD | Standard}

### Tareas Completadas
- [x] {tarea 1.1 descripción}
- [x] {tarea 1.2 descripción}

### Archivos Modificados
| Archivo | Acción | Qué se hizo |
|---------|--------|-------------|
| `path/to/file.ext` | Creado | {breve descripción} |
| `path/to/other.ext` | Modificado | {breve descripción} |

### Tests (sólo modo TDD)
| Tarea | Archivo de Test | RED (fail) | GREEN (pass) | REFACTOR |
|-------|-----------------|------------|--------------|----------|
| 1.1 | `path/to/test.ext` | ✅ Falló de acuerdo a lo esperado | ✅ Passed | ✅ Clean |
| 1.2 | `path/to/test.ext` | ✅ Falló de acuerdo a lo esperado | ✅ Passed | ✅ Clean |

{Omite esta sección si se usa el modo standard.}

### Desvíos Respecto del Diseño
{Enumera cualquier punto en el que la implementación haya diferido de `design.md` y el motivo.
Si no hay desviaciones, indica: "Ningun desvío — la implementación coincide con el diseño."}

### Problemas detectados
{Enumera cualquier problema descubierto durante la implementación.
Si no hubo ninguno, indica: "Ninguno."}

### Tareas pendientes
- [ ] {siguiente tarea}
- [ ] {siguiente tarea}

### Estado
{N}/{total} tareas completadas. {Listo para el siguiente lote / Listo para verificación / Bloqueado por X}
```

## Reglas

- SIEMPRE lee las especificaciones antes de implementar — las especificaciones son tus criterios de aceptación
- SIEMPRE sigue las decisiones de diseño — no improvises un enfoque diferente
- SIEMPRE respeta los patrones y convenciones de código existentes en el proyecto
- En modo `openspec`, marca las tareas como completas en `tasks.md` A MEDIDA que avanzas, no al final
- Si descubres que el diseño es incorrecto o incompleto, ANÓTALO en tu resumen de retorno — no te desvíes silenciosamente
- Si una tarea queda bloqueada por algo inesperado, DETENTE y repórtalo
- NUNCA implementes tareas que no te hayan sido asignadas
- La carga de skills se maneja en el Paso 1 — sigue estrictamente cualquier skill cargada al escribir código
- Aplica cualquier regla `rules.apply` definida en `openspec/config.yaml`
- Si se detecta modo TDD (Paso 3), SIEMPRE sigue el ciclo RED → GREEN → REFACTOR — nunca omitas RED (escribir primero la prueba que falla)
- Al ejecutar pruebas durante TDD, ejecuta SOLO el archivo o suite de pruebas relevante, no toda la suite (por eficiencia)
- Devuelve un contenedor estructurado con: `status`, `executive_summary`, `detailed_report` (opcional), `artifacts`, `next_recommended` y `risks`