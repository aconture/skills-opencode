# 🤖 Arquitectura de Agentes y Gestión de Skills

Este repositorio utiliza una arquitectura de **Spec-Driven Development (SDD)** basada en el patrón de orquestación de **Agent Teams Lite**, diseñada para maximizar la eficiencia del contexto y garantizar resultados deterministas a través de sub-agentes especializados.

El repositorio tiene dos funcionalidades:

## skills
Skills globales para usar en Antigravity. Almacenando en el path correcto, también pueden usarse en otros agentes.

## Antigravity Agent Teams

Esquema de agentes SDD que son invocados como skills, adaptado de https://github.com/Gentleman-Programming/agent-teams-lite.git para usar estrictamente en Antigravity (si querés algo agnóstico, andá al repo de agent-teams-lite)

## Estructura de directorios:
Ver README.md de este repositorio

## 🧠 El Orquestador (`sdd-orchestrator.md`)
El orquestador actúa como un **COORDINADOR**, no como un ejecutor. Su función principal es mantener un hilo de conversación ligero con el usuario y delegar todas las tareas técnicas (lectura/escritura de código, análisis, tests) a sub-agentes o fases basadas en skills.
- **Regla de Oro:** Nunca realiza trabajo real inline para evitar el ruido cognitivo y la pérdida de estado por compactación de contexto.

## 🚀 Cómo se invoca el orquestador

El agente `orchestrator` se declara como agente **primario** en `.opencode/opencode.json`:

```json
{
  "agent": {
    "orchestrator": {
      "mode": "primary",
      "description": "Coordinador SDD: orquesta las skills sdd-* y delega todo el trabajo pesado a subagentes via task",
      "prompt": "{file:./rules/sdd-orchestrator.md}",
      "permission": {
        "task": { "*": "allow" },
        "skill": { "*": "allow" }
      }
    }
  }
}
```

Se invoca por dos vías:

1. **Como agente activo (Tab en la TUI):** Al ser `primary`, se cicla con la tecla **Tab** hasta ponerlo como agente de la conversación. Desde ese momento toda interacción pasa por el coordinador.

2. **Vía comandos `/sdd-*` (autocompletado con Tab):** Los comandos declarados en `.opencode/command/` (`/sdd-init`, `/sdd-explore`, `/sdd-new`, `/sdd-continue`, `/sdd-ff`, `/sdd-apply`, `/sdd-verify`, `/sdd-archive`) se escriben y autocompletan en la TUI. Cada comando inyecta los `$ARGUMENTS` del usuario y arranca al orquestador indicándole leer `.opencode/rules/sdd-orchestrator.md` antes de actuar.

### Criterios de cuándo se invoca

- **Pregunta sencilla** → el orquestador responde brevemente si ya conoce la respuesta; si no, delega.
- **Tarea pequeña** (hotfix, archivo simple, corrección rápida) → delega a un sub-agente o ejecuta una skill inline.
- **Funcionalidad sustancial / refactor** → sugiere y ejecuta el flujo SDD: `/sdd-new {cambio}`.

En todos los casos el orquestador **NO ejecuta** trabajo técnico inline: respeta el gráfico de dependencias de `sdd-orchestrator.md`, lanza sub-agentes con el tool `task` e incluye en cada prompt la sección obligatoria **SKILL LOADING** (lectura de `.atl/skill-registry.md`). Al final de cada fase sintetiza el resultado con el contrato: `status`, `executive_summary`, `artifacts`, `next_recommended`, `risks`.

## 📋 Registro de Habilidades (`.atl/skill-registry.md`)
Es la **infraestructura central** que conecta al orquestador con el conocimiento procedural del repositorio. 
- **Función:** Actúa como un catálogo dinámico que mapea **Triggers (Disparadores) | Nombre de la Skill | Ruta Absoluta**.
- **Protocolo:** Todo sub-agente debe consultar este registro como su **primer paso obligatorio** para identificar qué habilidades son relevantes para su tarea actual.

## 🛠️ Skills Especializadas (`SKILL.md`)
Las habilidades son unidades de conocimiento encapsulado que permiten a los agentes ejecutar tareas complejas siguiendo metodologías específicas.
- **Anatomía:** Cada archivo `SKILL.md` combina metadatos (YAML frontmatter) con instrucciones técnicas en Markdown.
- **Carga Bajo Demanda:** Solo se cargan las habilidades que coinciden con el disparador de la tarea, manteniendo la ventana de contexto del sub-agente limpia y enfocada.

## 🔄 Protocolo de Ejecución
1. **Delegación:** El orquestador identifica una necesidad técnica y lanza un sub-agente con un contexto fresco.
2. **Descubrimiento:** El sub-agente lee el `.atl/skill-registry.md` para "aprender" las reglas del proyecto.
3. **Ejecución:** El sub-agente carga las skills necesarias (ej. `sdd-init`, `sdd-spec`) y realiza la tarea.
4. **Persistencia:** Los resultados y descubrimientos se guardan directamente en el backend de archivos (**OpenSpec**) para asegurar que la "fuente de verdad" sea compartida entre fases.

---


*Built based on Agent Teams Lite — Because building without a plan is just vibe coding with extra steps.*