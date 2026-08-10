## name: Portabilidad
1. A menos que te indique lo contrario, haz que el codigo se pueda portar a cualquier directorio, es decir que no tenga que configurar ningun path para que puedan correr cualquiera de los scripts que realizas

2. en el codigo incluye las lineas necesarias para importar librerias y dependencias, de manera que si quiero ejecutar el codigo en otra pc, al ejecutar por primera vez se disparen las instalaciones de librerias y dependencias necesarias.

3. usa siempre librerias que puedan ser portadas a windows o linux indistintamente.

4. Genera un log en un archivo "log.txt" con las operaciones importantes. Cada ejecución del script separala con dos líneas completas del caracter "="

## Código Legible y Mantenible
**Misión:** Aplicar principios de legibilidad y buenas prácticas de Clean Code. Úsalo siempre antes de finalizar una refactorización o creación de funciones.
Al activar esta skill, debes asegurar que todo el código generado cumpla con los siguientes estándares de legibilidad:

### Reglas de Redacción
1. **Nombres Descriptivos:** No utilices abreviaturas (ej. usa `userAuthenticationStatus` en lugar de `uAuth`).
2. **Funciones Pequeñas:** Cada función debe realizar una única tarea y no exceder las 20 líneas de código.
3. **Comentarios de Valor:** No expliques *qué* hace el código si es obvio; explica el *por qué* en fragmentos complejos.
4. **Estructura Clara:** - Utiliza guard clauses para evitar el anidamiento excesivo de `if/else`.
   - Mantén un orden lógico: constantes arriba, seguidas de funciones principales y funciones auxiliares al final.
5. **Codigo entendible** - debe prevalecer el código entendible por un junior, por sobre el código complejo, aún si se tiene que sacrificar performance
6. **Idioma de los comentarios:** Comenta siempre en español.
7. **Idioma de las variables, constantes y funciones:** Siempre en inglés, usando buenas prácticas para nombrar estos artefactos.

#### Ejemplo de Salida Esperada
- **Mal:** `const d = new Date(); // fecha actual`
- **Bien:** `const currentServerTime = new Date();`
