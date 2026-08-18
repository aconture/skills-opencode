---
name: update-readme
description: >
  Actualiza o crea el README.md de un proyecto documentando su uso y configuración paso a paso.
  Trigger: cuando el usuario diga "armar/crear/actualizar/mejorar/reescribir el README", "documenta la
  aplicación", "explica cómo se usa y configura cada parámetro", o pida documentación de uso del proyecto.
  Explora el codebase, describe cómo se usa la aplicación y documenta cada parámetro de configuración,
  en el idioma del proyecto.
license: MIT
metadata:
  author: AGCC
  version: "1.0"
---

# Actualizar README.md

## Propósito

Eres el responsable de producir un README.md claro, completo y fiel al código real. El objetivo es que
cualquier persona pueda **instalar, configurar y usar** la aplicación sin tener que leer el código fuente.

Tu tarea imita el flujo que produjo el README de "Detector Incendios": explorar el proyecto a fondo,
entender qué hace la aplicación, y documentar el uso y la configuración **paso a paso** y **parámetro
por parámetro**.

## Regla de oro

**Nunca inventes.** Todo lo que escribas en el README (nombres de parámetros, valores por defecto,
comandos, modos de uso, comportamientos) debe estar verificado en el código o en los archivos de
configuración reales. Si no podés verificar algo, no lo afirmes.

## Paso 1: Explorar el proyecto

Antes de escribir UNA sola línea, explorá el repositorio para entender qué hace:

```
Lista el árbol de archivos del proyecto (ignorando .git, node_modules, __pycache__, etc.)
├── Lee el README actual (si existe) para preservar estructura, tono y secciones útiles
├── Lee los scripts / entry points principales (main.py, *.py, index.js, server.py, etc.)
├── Lee todos los archivos de configuración (config.ini, .env.example, settings.py, *.toml, etc.)
├── Lee .gitignore (para saber qué NO se versiona y qué archivos se generan)
├── Lee manifests (requirements.txt, pyproject.toml, package.json, Dockerfile, docker-compose.yml)
├── Lee documentación existente: openspec/, docs/, AGENTS.md, README de subcarpetas
└── Examina los archivos de datos de ejemplo para entender el formato (si aplica)
```

Consejos de eficiencia:
- Leé los archivos clave en paralelo (mismo mensaje, varias llamadas a Read).
- Usá Grep/Glob para localizar rápidamente funciones, flags de CLI, secciones de config o `argparse`.
- Si el proyecto tiene skills SDD/openspec, las especificaciones describen el comportamiento previsto:
  son una fuente fiel de qué hace cada modo y parámetro.

## Paso 2: Entender la aplicación

Extraé del código estos datos concretos:

| Qué | Dónde buscarlo |
|-----|----------------|
| Qué hace la app (propósito en 1-2 frases) | docstring del script, spec.md, comentarios de cabecera |
| Cómo funciona (flujo) | orden de llamadas en main() |
| Requisitos (versión de runtime) | `Requiere: Python 3.8+`, package.json engines, pyproject, Dockerfile |
| Dependencias | imports, requirements.txt (¡marcá si solo usa stdlib!) |
| Puntos de entrada / modos de CLI | flags de sys.argv, argparse, `--help` |
| Parámetros de configuración | cada `cfg.get(...)`, `os.environ.get(...)` con su fallback |
| Secretos / archivos no versionados | .gitignore + comentarios del código |
| Artefactos generados | logs, estado, salidas (detector.log, alertados.json, *.csv, etc.) |

Anotá para CADA parámetro de configuración: **sección, clave, tipo, valor por defecto (fallback),
ejemplo y descripción de qué hace / qué pasa si se cambia**.

## Paso 3: Detectar el idioma y el tono

- Escribí el README **en el idioma del proyecto**: si el código, los comentarios y los logs están en
  español, escribí en español; si están en inglés, en inglés.
- Mantené el tono directo y práctico, en segunda persona ("configurás", "ejecutá", "verificá").
- Respetá las convenciones de estilo existentes (ej. tildes si el código las usa).

## Paso 4: Escribir el README

### Encoding

- Escribí siempre el README en **UTF-8**.
- Si el README actual es binario / UTF-16 o ilegible, reemplazalo por un archivo UTF-8 limpio.
- Si hay un README existente con buen contenido, actualizalo incrementando: no borres secciones útiles
  sin motivo.

### Estructura recomendada (adaptala a cada proyecto)

```markdown
# <Nombre del proyecto>

<Descripción corta: qué hace la app y en 1-2 líneas>

---

## Cómo funciona            <- flujo general (pasos numerados), si aporta claridad

## Requisitos               <- runtime, dependencias, cuentas/servicios necesarios

## Archivos del proyecto    <- tabla: archivo | rol | ¿se versiona?

## Configuración paso a paso <- secuencia 1..N, cada paso con su resultado esperado
                              (crear archivos, obtener claves, instalar, verificar)

## Referencia de parámetros  <- tabla por archivo/sección: clave | tipo | ejemplo | descripción

## Uso — modos de ejecución  <- cada modo/flag con su comando y qué hace
                              (incluí los ejemplos de comando en bloques de código)

## Ejemplo de salida          <- muestra real o representativa de la alerta/reporte/log

## Deduplicación / estado     <- si la app persiste estado (qué archivo, cómo funciona)

## Logs                        <- si genera logs: dónde y ejemplo de formato

## Resolución de problemas    <- tabla: problema | causa probable | solución

## Notas                      <- limitaciones, fuentes de datos, automatización sugerida
```

### Reglas de contenido

- **Parámetros:** documentá TODOS los parámetros configurables en una tabla por archivo/sección.
  Columnas mínimas: clave, tipo, ejemplo, descripción. Indicá cuál es el valor por defecto.
- **Secretos:** documentá qué archivos contienen credenciales y advertí que **no deben versionarse**.
  En los ejemplos de configuración usá **placeholders** (ej. `TU_MAP_KEY_AQUI`), NUNCA credenciales reales.
- **Pasos accionables:** numerá los pasos de configuración, cada uno con un resultado verificable
  (ej. "Corré este comando para verificar que funciona").
- **Modos/CLI:** documentá cada modo con su comando exacto y una descripción de su comportamiento,
  incluidas las diferencias (qué requiere cada uno, qué NO hace, precedencias entre flags).
- **Bordes y errores:** documentá los casos de error más comunes (falta de config, clave inválida,
  archivos ausentes) y su mensaje en logs, tal como aparecen en el código.
- **Ejemplos:** mostrá salidas representativas con formato real del script (encabezados, líneas de log,
  estructura de la alerta).

## Paso 5: Verificar

Antes de terminar:

1. Releé el README y contrastá cada afirmación con el código:
   - ¿Los nombres de secciones/claves de config coinciden EXACTAMENTE con los reales?
   - ¿Los valores por defecto coinciden con los fallbacks del código?
   - ¿Los comandos y flags son los reales?
   - ¿Falta algún parámetro configurable sin documentar?
2. Confirmá que el archivo quedó en UTF-8 y se renderiza correctamente (bloques de código, tablas).
3. No corras comandos que ejecuten la app a menos que el usuario lo pida (los modos de prueba de la
   app pueden enviar mails o tocar APIs; solo documentalos).

## Retorno

Cuando termines, comunicá brevemente qué hiciste: archivo actualizado/creado y las secciones
principales que quedaron documentadas.