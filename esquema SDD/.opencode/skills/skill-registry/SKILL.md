---
name: skill-registry
description: >
   Crea o actualiza el registro de skills del proyecto actual. Analiza las skills definidas por el usuario y las convenciones del proyecto, y escribe el archivo .atl/skill-registry.md.
   Trigger: cuando el usuario dice “update skills”, “skill registry”, “actualizar skills”, “update registry”, o después de instalar o eliminar skills.
license: MIT
metadata:
  author: AGCC took from gentleman-programming
  version: "1.0"
---

## Propósito

Generas o actualizas el **registro de skills** — un catálogo de todas las habilidades disponibles (a nivel de usuario y a nivel de proyecto) que los sub‑agentes leen antes de comenzar cualquier tarea. Esto garantiza que cada sub‑agente sepa qué skills existen y pueda cargar las relevantes.

## Cuándo ejecutar

- Después de instalar o eliminar skills
- Después de configurar un proyecto nuevo
- Cuando el usuario solicita explícitamente actualizar el registro
- Como parte de `sdd-init` (utiliza esta misma lógica)

## Qué hacer

### Paso 1: Escanear skills del usuario

1. Busca (glob) archivos `*/SKILL.md` en TODOS los directorios de skills conocidos.
   Revisa **todas** las rutas listadas a continuación — escanea **TODAS las que existan**, no solo la primera coincidencia:

   **Nivel usuario (skills globales):**
   - `~/.gemini/skills/` — Gemini CLI
   - `~/.gemini/antigravity/skills/` — Antigravity CLI
   - El directorio padre de este archivo de skill (comodín para cualquier herramienta)

   **Nivel proyecto (skills del workspace):**
   - `{project-root}/.opencode/skills/` — OpenCode (workspace)
   - `{project-root}/.gemini/skills/` — Gemini CLI
   - `{project-root}/.agent/skills/` — Antigravity (workspace)
   - `{project-root}/skills/` — Genérico

2. **Si la sección de `sdd-*` (skills del flujo SDD u orquestación):** No van en "Skills del usuario" (son de codificación o tareas); van en su propia sección **"Skills SDD (Orquestación)"**. Para cada una, lee únicamente el *frontmatter* y extrae `name` + trigger de `description`. Omitir `_shared` (no es una skill).
3. **También OMITIR `skill-registry`** de la sección de usuario — es esta misma skill. Sí inclúyela en la sección "Skills SDD (Orquestación)".  
4. **Eliminar duplicados** — si el mismo nombre de skill aparece en múltiples ubicaciones, conserva la versión a nivel proyecto (más específica). Si ambas son de nivel usuario, conserva la primera encontrada.  
5. Para cada skill encontrada, lee únicamente el *frontmatter* (las primeras 10 líneas) para extraer:
   - El campo `name`
   - El campo `description` → extraer el texto del disparador (después de "Trigger:" en la descripción)  
6. Construye una tabla con las columnas: **Trigger | Nombre de la skill | Ruta**.  
   **IMPORTANTE**: Para las **skills de nivel proyecto**, usa rutas **relativas** a la raíz del proyecto. Para las **skills globales** (nivel usuario), usa rutas **absolutas** (o con `~` si es soportado).

### Step 2: Scan Project Conventions

1. Check the project root for convention files. Look for:
   - `agents.md` or `AGENTS.md`
   - `GEMINI.md`
2. **If an index file is found** (e.g., `agents.md`, `AGENTS.md`): READ its contents and extract all referenced file paths. These index files typically list project conventions with paths — extract every referenced path and include it in the registry table alongside the index file itself.
3. For non-index files (`.cursorrules`, `CLAUDE.md`, etc.): record the file directly.
4. The final table should include the index file AND all paths it references — zero extra hops for sub-agents.

### Paso 2: Escanea convenciones del proyecto

1. Revisa la raíz del proyecto en busca de archivos de convenciones. Busca:
   - `agents.md` o `AGENTS.md`
   - `GEMINI.md`
2. **Si se encuentra un archivo índice** (por ejemplo, `agents.md`, `AGENTS.md`): LEE su contenido y extrae **todas las rutas de archivos referenciadas**. Estos archivos índice suelen listar las convenciones del proyecto junto con sus rutas — extrae **cada ruta mencionada** e inclúyela en la tabla del registro junto con el propio archivo índice.
3. Para archivos **no índice** (`.cursorrules`, `CLAUDE.md`, etc.): registra directamente el archivo.
4. **IMPORTANTE**: Usa siempre rutas **relativas** a la raíz del proyecto para todas las convenciones.
5. La tabla final debe incluir el archivo índice Y todas las rutas que referencia — **cero saltos adicionales** para los sub‑agentes.

### Paso 3: Escribe el Registro

Construye el markdown del registro:

```markdown
# Registro de la Skill

Como PRIMER paso antes de comenzar cualquier trabajo, identifica y carga las skills relevantes para tu tarea desde este registro.

## Skills SDD (Orquestación)

Skills del flujo SDD, usadas por el orquestador y los subagentes. Rutas relativas a la raíz del proyecto.

| Trigger | Skill | Ruta |
|---------|-------|------|
| {trigger extraído del frontmatter} | {nombre de la skill sdd-*} | `.opencode/skills/{nombre}/SKILL.md` |
| ... | ... | ... |

## Skills del usuario

| Trigger | Skill | Ruta |
|---------|-------|------|
| {trigger extraído del frontmatter} | {nombre de la skill} | {ruta (relativa si es de proyecto, absoluta si es global)} |
| ... | ... | ... |

## Convenciones del proyecto

| Archivo | Ruta | Notas |
|---------|------|-------|
| {archivo índice} | {ruta} | Índice — referencia los archivos siguientes |
| {archivo referenciado} | {ruta extraída} | Referenciado por {archivo índice} |
| {archivo independiente} | {ruta} | |

Lee los archivos de convenciones listados arriba para conocer los patrones y reglas específicos del proyecto.  
Todas las rutas referenciadas ya han sido extraídas — no es necesario leer los archivos índice para descubrir más.

```

### Paso 4: Persiste el registro

**Este paso es OBLIGATORIO — NO lo omitas.**

#### A. Escribir siempre el archivo (disponibilidad garantizada):

Crea el directorio `.atl/` en la raíz del proyecto si no existe y luego escribe:

```
.atl/skill-registry.md
```

### Step 5: Resumen de la Devolución

```markdown
## Registro de Skill Actualizado

**Proyecto**: {nombre del proyecto}
**Ubicación**: .atl/skill-registry.md

### Skills del usuario encontradas
| Skill | Trigger |
|-------|---------|
| {nombre} | {trigger} |
| ... | ... |

### Convenciones del proyecto encontradas
| Archivo | Ruta |
|---------|------|
| {archivo} | {ruta} |

### Próximos pasos
Los sub‑agentes cargarán automáticamente las skills relevantes desde este registro.
Para actualizarlo después de instalar o eliminar skills, vuelve a ejecutar este proceso.
```

## Reglas

- SIEMPRE escribe `.atl/skill-registry.md`, independientemente del modo de persistencia de SDD
- Excluye `_shared` de la sección "Skills del usuario"; las skills `sdd-*` (incluida `skill-registry`) van en la sección "Skills SDD (Orquestación)". Rutas relativas a la raíz del proyecto.
- Lee **solo** el frontmatter (primeras 10 líneas) — NO leas el archivo completo de la skill
- Incluye **TODOS** los archivos índice de convenciones encontrados (no solo el primero)
- Si no se encuentran skills o convenciones, escribe un registro vacío (para evitar que los sub‑agentes pierdan tiempo buscando)
- Agrega `.atl/` al `.gitignore` del proyecto si existe y `.atl` no está ya listado
- **PORTABILIDAD**: Prioriza siempre el uso de rutas relativas para cualquier recurso que se encuentre dentro del espacio de trabajo del proyecto.
