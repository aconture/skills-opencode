---
name: thisProject-changelog
description: >
  Manages changelog entries for ThisProject following keepachangelog.com format.
  Trigger: When creating, modifying, deleting features, or solving fixes.
license: Apache-2.0
metadata:
  author: AGCC
  version: "1.1"
---

## Propósito
Eres un experto en gestión de versiones y documentación. Tu objetivo es mantener el archivo `CHANGELOG.txt` en la raíz del proyecto siguiendo estrictamente los lineamientos de [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## Estructura Mandatoria del Archivo

El archivo DEBE comenzar siempre con:
```markdown
# Changelog

## [UNRELEASED] (ThisProject UNRELEASED)
```

**PROHIBICIÓN CRÍTICA:**
- NUNCA agregues una fecha sola al principio del archivo (ej: `2026-05-06`).
- NUNCA uses prefijos entre corchetes como `[MEJORA]`, `[FIX]` o `[NUEVO]`. Usa las secciones correspondientes.
- NUNCA rompas el orden de las secciones.

## Reglas de Formato

### 1. Secciones y Emojis (OBLIGATORIO)

Debes usar exactamente estos encabezados para las categorías de cambio:

| Sección | Emoji Mandatorio | Uso |
|---------|-----------------|-----|
| Added | `### 🚀 Added` | Nuevas funcionalidades, reportes, endpoints |
| Changed | `### 🔄 Changed` | Modificaciones a lógica existente, refactorización |
| Deprecated | `### ⚠️ Deprecated` | Funcionalidades marcadas para eliminación |
| Removed | `### ❌ Removed` | Funcionalidades eliminadas (solo en versiones MAJOR) |
| Fixed | `### 🐞 Fixed` | Corrección de errores y bugs |
| Security | `### 🔐 Security` | Parches de seguridad |

### 2. Orden de las Secciones (SIEMPRE este orden)
Si una sección no existe, créala en su lugar correspondiente:
`Added` → `Changed` → `Deprecated` → `Removed` → `Fixed` → `Security`.

### 3. Formato de las Entradas
- **Añade las nuevas entradas ARRIBA (al principio)** de cada sección, no al final.
- Deja una línea en blanco después del encabezado de sección.
- Deja una línea en blanco entre secciones.
- Máximo 2 líneas por entrada.
- No uses punto final.
- No empieces con verbos redundantes (ej: no pongas "Añadida la función X" en la sección Added; pon "Función X: descripción").
- **Estructura recomendada:** `Módulo: descripción clara del cambio`.

## Gestión de Versiones (SemVer)

| Tipo de Cambio | Incremento | Ejemplo |
|----------------|------------|---------|
| Bug fixes / Patches | PATCH (x.y.**Z**) | 1.10.1 → 1.10.2 |
| Nuevas funciones (retrocompatibles) | MINOR (x.**Y**.0) | 1.10.2 → 1.11.0 |
| Cambios disruptivos (breaking changes) | MAJOR (**X**.0.0) | 1.11.0 → 2.0.0 |

**NOTA:** Las versiones publicadas son INMUTABLES. Solo edita la sección `[UNRELEASED]`.

## Procedimiento para añadir una entrada

1.  **Identifica el Módulo:** (Dashboard, Ingesta, API, Documentación, etc.).
2.  **Identifica el Tipo de Cambio:** Selecciona la sección correcta (Added, Changed, Fixed, etc.).
3.  **Localiza el bloque `[UNRELEASED]`:** Si no existe, créalo después de `# Changelog`.
4.  **Inserta la entrada:** Al principio de la sección elegida con el emoji correspondiente.

## Ejemplos Correctos ✅

```markdown
### 🚀 Added
- Ingesta: Solapa "Resumen" con conteo de reservas por obra
- API: Nuevo endpoint /api/summary para reportes rápidos

### 🐞 Fixed
- Dashboard: Error de visualización en gráficos al filtrar por región
```

## Ejemplos Incorrectos ❌ (ERROR)

```markdown
# ERROR - Fecha en lugar de versión
2026-05-06
- [MEJORA] Agregado resumen

# ERROR - Verbo redundante y falta de emoji
### Added
- Agregado reporte de reservas.
```

## Nota de Migración
Si el archivo existente no sigue este formato (ej: tiene fechas al principio), DEBES reformatearlo íntegramente siguiendo estas reglas en tu primera edición.
