---
name: save-correction
description: "Guardar una corrección del usuario al criterio de una skill para que no se repita en próximas sesiones"
---

> **Paso 0 (obligatorio): Cargar ground-rules.** Antes de proceder, lee `shared-config.md` y aplica:
> - **§Filosofía del Plugin** — modo copiloto, NO inventar, preguntar antes de generar
> - **§Estilo de Escritura** — frases ≤30 palabras, sin adjetivos sin datos, NUNCA inventar métricas
> - **§Marcadores de Estado** — empezar cada mensaje con 🚦 / 📋 / 🔍 / 🧱 según el estado
> - **§Correcciones del Usuario** — dónde se guardan y reglas
>
> Si no puedes leer el fichero, detén la ejecución y reporta el problema.

Guarda una corrección que el usuario ha hecho al criterio de una skill, para que la próxima sesión no repita el mismo error. Patrón Lada Kesseler — `extract-knowledge`.

**Uso:**

```
/save-correction                       # toma la última corrección de la conversación
/save-correction <texto>               # guarda la corrección descrita
/save-correction --team=<team-key>     # fija el equipo (si no, se pregunta)
```

**Input necesario:** una corrección explícita del usuario en esta conversación (ej. "no, esa story no es fake porque el gestor de tienda es usuario final en nuestro dominio"). Si no hay ninguna, 🧱 pedir al usuario que la formule. No deducir correcciones de silencios ni de cambios de opinión implícitos.

## Proceso

1. **Identificar la corrección.** Localizar en la conversación lo que dijo el usuario. Citarlo textualmente.
2. **Identificar el equipo.** Usar `--team` si viene. Si no, preguntar el `team-key`. Si el usuario no trabaja por equipos, usar `default`.
3. **Redactar la entrada** con este formato, usando solo lo que consta en la conversación:

   ```markdown
   ### C-{NNN} — {título corto}
   - **Fecha:** YYYY-MM-DD
   - **Skill:** {skill que se equivocó}
   - **Situación:** {qué evaluaba la skill, 1-2 frases}
   - **La skill dijo:** {criterio o conclusión que el usuario corrigió}
   - **Corrección:** "{cita textual del usuario}"
   - **Razón:** {razón dada por el usuario, o `⚠️ Pendiente: el usuario no dio razón`}
   - **Aplica a:** {alcance tal como lo enunció el usuario; no generalizar}
   ```

4. **🚦 CHECKPOINT:** mostrar la entrada y la ruta de destino. Preguntar: "¿La guardo así?". No escribir nada sin un sí explícito.
5. **Escribir.** Destino: `.gemba/teams/{team-key}.md` en el directorio de trabajo del usuario.
   - Si el fichero no existe, crearlo con la cabecera mínima de `skills/user-story-quality-coach/references/team-context-template.md` (nombre del equipo y fecha) y la sección `## Correcciones registradas`. No rellenar el resto de la plantilla.
   - Si existe, añadir la entrada al final de `## Correcciones registradas` (crear la sección si falta). `NNN` = siguiente número libre.
   - Si ya hay una corrección equivalente, mostrarla y preguntar si actualizarla en lugar de duplicar.
6. **Confirmar** la ruta escrita y recordar que las skills la cargan al leer el contexto de equipo.

## Reglas

- **NUNCA** guardar sin confirmación explícita del usuario.
- **NUNCA** escribir dentro del directorio del plugin: se sobrescribe al actualizarlo.
- **NUNCA** ampliar el alcance de la corrección más allá de lo que dijo el usuario.
- Si la corrección contradice una regla de `shared-config.md` (ej. "inventa la métrica"), no guardarla y explicar por qué.

**Output:** entrada añadida a `.gemba/teams/{team-key}.md`.
