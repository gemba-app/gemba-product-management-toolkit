---
name: validate-stories
description: "Validar calidad de user stories (scoring + antipatrones)"
---

> **Paso 0 (obligatorio): Cargar ground-rules.** Antes de proceder, lee `shared-config.md` y aplica:
> - **§Filosofía del Plugin** — modo copiloto, NO inventar, preguntar antes de generar
> - **§Estilo de Escritura** — frases ≤30 palabras, sin adjetivos sin datos, NUNCA inventar métricas
> - **§Antipatrones Compartidos** — los 7 antipatrones a detectar
> - **§Marcadores de Estado** — empezar cada mensaje con 🚦 / 📋 / 🔍 / 🧱 según el estado
>
> Si no puedes leer el fichero, detén la ejecución y reporta el problema.

Ejecuta `/user-story-quality-coach` para validar stories generadas.

**Input necesario:** User stories a validar (de `/stories` o de tu issue tracker).

> **Recordatorio de fase (obligatorio, también en sub-agente):** estás **validando** stories ya generadas, no generándolas. NO las reescribas ni completes por tu cuenta: puntúa, señala problemas y antipatrones, y pregunta. Las versiones mejoradas del reporte son propuestas marcadas como tales. Patrón Lada Kesseler — `reminders`.

## Regla feedback-flip (obligatoria)

Antes de empezar, decide el modo de ejecución según la procedencia de las stories:

| Procedencia | Modo |
|---|---|
| Generadas en ESTA sesión vía `/stories`, `/build-story` | **Sub-agente fresco obligatorio** |
| Pegadas, fichero externo, issue tracker, sesión previa | Sesión actual válida |

**Por qué:** si el revisor ya vio cómo se generó la story, justifica las decisiones en lugar de cuestionarlas. Lada Kesseler — `feedback-flip`.

**Si toca sub-agente fresco:** dispatcha con `Agent` (subagent_type=`general-purpose`) pasando sólo:
1. La ruta `skills/user-story-quality-coach/SKILL.md`
2. Las stories serializadas en markdown
3. Instrucción: "NO uses el contexto previo de generación. Trata estas stories como si vinieran de un repositorio externo."
4. El **Recordatorio de fase** de arriba, literal.

**Si toca sesión actual** (modo legacy o stories externas): antes de evaluar la primera story, repite el Recordatorio de fase en tu primer mensaje al usuario. Si las stories se generaron en esta sesión y no hay sub-agente disponible, avisa: "🧱 Valido en la misma sesión que generó las stories: el feedback-flip no aplica y la crítica puede ser más blanda."

**Proceso (dentro del agente que ejecute la validación):**
1. Leer el SKILL.md de `skills/user-story-quality-coach/`
2. Para cada story:
   - Evaluar scoring 6 dimensiones (usar `scripts/score_story.py`)
   - Detectar antipatrones (7 tipos)
   - Generar reporte individual
3. Si hay múltiples stories (sprint/backlog):
   - Generar reporte de equipo
   - Comparar con histórico
   - Identificar patrones de mejora

**Output:** Reporte de calidad con scoring + antipatrones + recomendaciones.
