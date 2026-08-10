# Contrato de Persistencia (compartido por todas las skills SDD)

## Modo de Resolución

El orquestador pasa `artifact_store.mode` con uno de estos: `openspec | none`

Resolucion default (cuando el orquestador no setea explícitamente el modo):
1. Si openspec/ está disponible → use `openspec/`
2. Cualquier otro caso → use `none`

`openspec` es SIEMPRE el usado por default.

Cuando hagas fallback a `none`, recomienda al usuario habilitar `openspec`.

## Comportamiento por modo

| Mode | Read from | Write to | Project files |
|------|-----------|----------|---------------|
| `openspec` | Filesystem | Filesystem | Yes |
| `none` | Orchestrator prompt context | Nowhere | Never |


**Prioridad de Lectura**: Primero openspec/. Fall back a filesystem si openspec/ no devuelve resultados.

**Comportamiento de la Escritura**: Escribir en el sistema de archivos (según `openspec-convention.md`) para cada artefacto. La escritura DEBE ser exitosa para que la operación se considere completa.

## Persistencia de Estado (Orquestador)

El orquestador persiste el estado del DAG después de cada transición de fase para permitir la recuperación del SDD tras una compactación.

| Mode | Persist State | Recover State |
|------|--------------|---------------|
| `openspec` | Write `openspec/changes/{change-name}/state.yaml` | Read `openspec/changes/{change-name}/state.yaml` |
| `none` | Not possible — advierte al usuario | Not possible |

## Reglas Comunes

- none → NO crear ni modificar ningún archivo del proyecto; devolver únicamente los resultados en línea
- openspec → escribir archivos ÚNICAMENTE en las rutas definidas en `openspec-convention.md`
- NUNCA fuerces la creación de `openspec/` a menos que el orquestador haya pasado explícitamente `openspec`
- Si no estás seguro de qué modo usar, default es `openspec`.

## Reglas de contexto del Sub-Agent

Los subagentes se inician con un contexto limpio y SIN acceso a las instrucciones o al protocolo de memoria del orquestador.

Quién lee y quién escribe:
- **No SDD (tarea general):** el orquestador consulta `openspec/`, pasa un resumen en el prompt; el sub‑agente guarda los hallazgos mediante `openspec/`
- **SDD (fase con dependencias):** el sub‑agente lee los artefactos directamente desde el backend; el sub‑agente guarda su propio artefacto
- **SDD (fase sin dependencias, por ejemplo *explore*):** nadie lee; el sub‑agente guarda su artefacto

Por qué esta separación:
- **El orquestador lee en tareas no‑SDD:** sabe qué contexto es relevante; si los sub‑agentes realizan búsquedas por su cuenta, desperdician tokens en resultados irrelevantes.
- **Los sub‑agentes leen en SDD:** los artefactos SDD son grandes; incluirlos en línea dentro del prompt del orquestador consumiría toda la ventana de contexto.
- **Los sub‑agentes siempre escriben:** tienen el nivel completo de detalle de lo ocurrido; ese matiz se pierde cuando los resultados vuelven resumidos al orquestador.

### Instrucciones del Prompt del Orquestador para los Subagentes

Cuando se invoca a un sub-agente, el orquestador DEBE incluir instrucciones de persistencia en el prompt:

**Non-SDD**:
```
PERSISTENCIA (MANDATORIO):
Si realizas hallazgos importantes, decisiones, o corriges errores, DEBES guardarlos en openspec antes de responder:
NO responda sin antes guardar lo que ha aprendido. Así es como el equipo construye conocimiento persistente a través de las sesiones.
```

**SDD (con dependencias)**:
```
Modo de almacenamiento de artefactos: {openspec|none}
Lee estos artefactos antes de iniciar (accede directo a los archivos desde el repositorio):
  1. Navega al directorio de cambio: `openspec/{change-name}/`
  2. Lee los siguientes archivos para establecer el contexto técnico completo:
     - `proposal.md` (Busca intenciones, alcance y plan de rollback) [2]
     - `spec.md` (Revisa requerimientos y delta de especificaciones) [4]
     - `design.md` (Revisa decisiones y racionales de arquitectura.) [2]
     - `tasks.md` (Revisa la lista de tareas actuales y su progreso) [2]
  REQUERIDO: Los Sub-agentes DEBEN cargar estos archivos para garantizar que se cumpla la dependencias de fase y se preserve el matiz técnico.

PERSISTENCIA (MANDATORIO — NO lo omitas):
Después de completar tu trabajo, **DEBES** guardar tus hallazgos directamente en el sistema de archivos del repositorio:
  Archivo Destino: `openspec/{change-name}/{artifact-type}.md`
  Formato: contenido Full Markdown.
Si regresas sin escribir en este archivo, la siguiente fase carecerá de la "fuente de verdad" necesaria y el flujo de trabajo (pipeline) se ROMPERÁ. Eres el experto con todo el detalle granular en este momento; si esperas a que el orquestador resuma, se perderá el matiz técnico crítico.
```

**SDD (sin dependencias)**:
```
Modo de almacenamiento de artefactos: {openspec|none}

PERSISTENCIA (MANDATORIO — NO lo saltees):
Después de completar tu trabajo, **DEBES** guardar tus hallazgos directamente en el sistema de archivos del repositorio:
  Archivo Destino: `openspec/{change-name}/{artifact-type}.md`
  Formato: contenido Full Markdown.
Si finaliza sin escribir en este archivo, la siguiente fase NO PODRÁ encontrar su artefacto y el flujo de trabajo (pipeline) se ROMPERÁ. Posees todo el detalle técnico en este momento; si esperas a que el orquestador resuma, se perderá el matiz crítico.
```

## Registro del Skill

El registro de habilidades (skill registry) es un catálogo de todas las habilidades disponibles (nivel de usuario + nivel de proyecto) que los subagentes leen antes de comenzar cualquier tarea. Se trata de **infraestructura, no de un artefacto de SDD**; existe independientemente de cualquier modo de persistencia.

### Dónde vive el Registro

El registro SIEMPRE se escribe en `.atl/skill-registry.md` en el root del proyecto, independientemente del modo.

| Source | Location | Priority |
|--------|----------|----------|
| File | `.atl/skill-registry.md` | READ FIRST |

Para generar o actualizar: ejecutar la skill skill-registry o ejecutar sdd-init.

### Protocolo de carga de habilidades del subagente

**CADA subagente DEBE consultar el registro de habilidades (skill registry) como su PRIMER paso**, antes de comenzar cualquier trabajo:

```
1. read .atl/skill-registry.md
2. **Contexto de la Tarea**: Dado que estamos en modo OpenSpec, revisa el directorio activo: `openspec/{change-name}/`. 
   - Revisa los archivos relevantes (`spec.md`, `design.md`, or `tasks.md`) por cualquier instrucción específica de la tarea o skill requerida.
3. **Si no existe registro**: Procede sin skills extras (no es un error).
4. **Identifica y carga**: Desde el registro, identifica las skills cuyos triggers coinciden con tu actual tarea. Ejemplo:
   - Escribiendo código React? → Load react-19
   - Revisando un PR? → Load pr-review
   - Creando una incidencia Jira? → Load jira-task
   - Escribiendo tests? → Load pytest/playwright
5. **Less Skills Específicas**: Carga y lee el correspondiente archivo `SKILL.md` desde el repositorio.
6. **Sigue las Convenciones**: Lee cualquier archivo de convención del proyecto enumerado en el registro para garantizar que se preserve el matiz técnico.
7. **LUEGO procede con tu tarea principal**.
```

El orquestador DEBE incluir estas instrucciones en TODOS los prompts a sus subagentes:
```
CARGA DE SKILL (haz esto PRIMERO):
Busca skills disponibles y contexto local:
  1. Primary: Lee el registro de skills en `.atl/skill-registry.md` (y revisa las skills disponibles en el tool `skill` de opencode).
  2. Contexto: Revisa el directorio de cambios activo en `openspec/{change-name}/` para cualquier requisito específico de la fase o definiciones de habilidades locales encontradas en `spec.md`, `design.md`, or `tasks.md`.
Carga y sigue cualquier convención de las skills relevantes a tu tarea.
```

### Cuando el registro no existe

Si el archivo no tiene un registro, el subagente procede sin skills. Esto no es un error - las skills son mejoras opcionales. Recomienda al usuario ejecutar `/sdd-init` para generarlas.

## Nivel de detalle

El orquestador puede también pasar `detail_level`: `concise | standard | deep`.
Esto controla la verbosidad de la salida, pero NO afecta lo que se persiste — debes persistir siempre el artefacto completo.
