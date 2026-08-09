---
description: Genera código legible y mantenible usando el lenguaje del stack definido por la arquitectura del Proyecto. Úsalo al crear o modificar código o realizar refactorizaciones.
mode: all
---

## Principios
- Aplicar principios de legibilidad y buenas prácticas de Clean Code.

## Reglas

### Reglas de Redacción
1. **Nombres Descriptivos:** No utilices abreviaturas (ej. usa `userAuthenticationStatus` en lugar de `uAuth`).

2. **Funciones Pequeñas:** Cada función debe realizar una única tarea y no exceder las 20 líneas de código.

3. **Comentarios de Valor:** No expliques *qué* hace el código si es obvio; explica el *por qué* en fragmentos complejos.

4. **Estructura Clara:** Utiliza guard clauses para evitar el anidamiento excesivo de `if/else`.
   Mantén un orden lógico: constantes arriba, seguidas de funciones principales y funciones auxiliares al final.

5. **Código entendible:** debe prevalecer el código entendible por un junior, por sobre el código complejo, aún si se tiene que sacrificar performance.

6. **Idioma de los comentarios:** Comenta siempre en español.

7. **Idioma de las variables, constantes y funciones:** Siempre en inglés, usando buenas prácticas para nombrar estos artefactos.

### Ejemplo de Salida Esperada
- **Mal:** `const d = new Date(); // fecha actual`
- **Bien:** `const currentServerTime = new Date();`

## Reglas de Portabilidad
1. A menos que se indique lo contrario, hace que el código se pueda portar a cualquier directorio: no se debe configurar ningún path para que corran cualquiera de los scripts generados.
2. En el código incluye las líneas necesarias para importar librerías y dependencias, de manera que si se quiere ejecutar el código en otro equipo, pc o servidor; al ejecutar por primera vez se disparen las instalaciones de librerías y dependencias necesarias.
3. Prioriza siempre el uso de librerías que puedan ser portadas a windows o linux indistintamente.

## Reglas de Log
1. Genera un log en un archivo "log.txt" durante la ejecución de los scripts, para reflejar partes relevantes del código.
2. Cada ejecución del script sepárala con dos líneas completas del caracter "=".