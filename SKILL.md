---
name: auto-research-notebooklm
description: >
  Investigación profunda usando NotebookLM via MCP. Produce entregables
  estructurados en Markdown (informes, mapas mentales, fuentes, etc.).

  ACTIVACIÓN EXPLÍCITA ÚNICAMENTE. Este skill NO se auto-activa.
  Solo se ejecuta cuando el usuario invoca explícitamente con frases como:
  "notebook research, investiga: [tema]"
  "usando el skill notebook research, investiga: [tema]"
  "ejecuta notebook research sobre [tema]"

  NO ACTIVAR bajo ninguna otra circunstancia, ni siquiera si el usuario
  dice "investiga", "busca información" u otras palabras de investigación
  sin mencionar explícitamente "notebook research" como prefijo.
tags: [research, notebooklm, mcp, reports, mindmap, deep-research]
version: "2.1"
---

# SKILL: Auto-Research con NotebookLM MCP

## Meta
- **Autor**: Eliú / Floydia
- **Requiere**: MCP server `notebooklm` activo y autenticado
- **Idioma de operación**: el mismo que use el usuario en su solicitud
- **Tiempo estimado**: 5–15 minutos según profundidad

---

## Goal

Transformar cualquier solicitud de investigación — desde una idea vaga hasta
una pregunta compleja — en un proyecto completamente documentado con:
fuentes verificadas y clasificadas, resúmenes en múltiples niveles de detalle,
mapa mental visual, preguntas abiertas para profundizar, y opcionalmente
audio/video overviews o presentaciones. Todo almacenado en una estructura
local reproducible.

---

## Inventario de herramientas MCP disponibles

Antes de ejecutar, ten presente las herramientas reales del servidor `notebooklm`:

| Herramienta MCP                    | Uso en este skill                                      |
|-------------------------------------|--------------------------------------------------------|
| `notebook_create`                   | Crear notebook dedicado al proyecto                    |
| `notebook_list`                     | Verificar si ya existe un notebook relevante           |
| `notebook_add_url`                  | Agregar URLs específicas como fuentes                  |
| `notebook_add_text`                 | Agregar contexto textual del usuario como fuente       |
| `notebook_add_local_file`           | Agregar PDFs o archivos locales como fuentes           |
| `notebook_add_drive`                | Agregar documentos de Google Drive                     |
| `research_start`                    | Iniciar búsqueda web o Drive (fast ≈30s / deep ≈5min) |
| `research_poll`                     | Monitorear estado de la investigación                  |
| `research_import`                   | Importar fuentes descubiertas al notebook              |
| `notebook_query`                    | Interrogar las fuentes cargadas con preguntas          |
| `report_create`                     | Generar reporte estructurado desde las fuentes         |
| `mind_map_generate` + `mind_map_save` | Generar y guardar mapa mental                        |
| `audio_overview_create`             | Generar podcast-resumen del notebook                   |
| `video_overview_create`             | Generar video-resumen del notebook                     |
| `slide_deck_create`                 | Generar presentación de diapositivas                   |
| `flashcards_create`                 | Generar tarjetas de estudio                            |
| `infographic_create`                | Generar infografía                                     |
| `data_table_create`                 | Generar tabla comparativa de datos                     |
| `studio_poll`                       | Verificar estado de artefactos generados               |

---

## Instructions

### FASE 0 — Activación y encuadre

**REGLA DE ACTIVACIÓN**: Este skill SOLO se ejecuta cuando el usuario lo
invoca explícitamente con una de estas formas:

```
"notebook research, investiga: [tema]"
"usando el skill notebook research, investiga: [tema]"
"ejecuta notebook research sobre [tema]"
"notebook research: [tema]"
```

Si el usuario dice "investiga X" SIN el prefijo "notebook research",
NO activar este skill — responder directamente o preguntar si quiere
usar el skill de investigación profunda.

**Al activarse:**

1. **Confirma el encuadre** en máximo 2 frases:
   > "🔬 Skill Notebook Research activado. Voy a guiarte por 4 fases para
   > investigar [tema]: intake → recopilación → análisis → entregables."

2. **Verifica autenticación MCP**: internamente llama `notebook_list` para
   confirmar que el servidor está operativo. Si falla, ejecuta `refresh_auth`
   y reintenta una vez. Si persiste el error, notifica al usuario con el
   mensaje exacto y propón continuar sin MCP (investigación manual).

---

### FASE 1 — Intake (Entendimiento del scope)

Haz estas preguntas **agrupadas en bloques lógicos** (no una a una, eso es
tedioso). Presenta los bloques como formulario conversacional:

**Bloque A — Tema y objetivo:**
1. ¿Cuál es el tema principal?
2. ¿Cuál es el objetivo final? _(aprender · crear contenido · tomar decisión · construir algo · otro)_
3. ¿Nivel de conocimiento actual? _(0 = nada · 5 = intermedio · 10 = experto)_

**Bloque B — Enfoque y restricciones:**
4. ¿Subtemas o ángulos prioritarios? _(si no tienes, dilo y yo propongo)_
5. ¿Restricciones? _(audiencia, idioma de fuentes, rango de fechas, región, presupuesto, etc.)_

**Bloque C — Formato de salida:**
6. ¿Qué entregables quieres? _(puedes elegir varios)_
   - 📄 Informe detallado + resumen ejecutivo _(default)_
   - 🧠 Mapa mental interactivo
   - 🎙️ Audio overview (podcast de ~10 min)
   - 🎬 Video overview
   - 📊 Presentación / slide deck
   - 🗂️ Flashcards / tarjetas de estudio
   - 📈 Infografía
   - 📋 Tabla comparativa de datos

**Reglas del intake:**
- Si el usuario responde en bloque, acepta todo y no re-preguntes lo ya respondido.
- Si una respuesta es ambigua, ofrece 2–3 opciones concretas:
  > "Cuando dices 'IA', ¿te interesa el ángulo técnico (modelos, arquitecturas),
  > el de negocios (casos de uso, ROI) o el ético/social?"
- Si el usuario dice "tú decide" o "lo que sea mejor", aplica defaults inteligentes
  y muéstralos para confirmación rápida.

**Al finalizar**, presenta un **brief de proyecto** así:

```
╔══════════════════════════════════════════════════╗
║  BRIEF DE INVESTIGACIÓN                          ║
╠══════════════════════════════════════════════════╣
║  Tema:        [tema]                             ║
║  Objetivo:    [objetivo]                         ║
║  Nivel:       [N]/10                             ║
║  Subtemas:    [lista]                            ║
║  Restricciones: [lista o "ninguna"]              ║
║  Entregables: [lista de formatos seleccionados]  ║
║  Nombre corto: [NOMBRE_PROYECTO]                 ║
╚══════════════════════════════════════════════════╝
```

Pide confirmación con: "¿Todo correcto? Responde **ok** para empezar la
investigación, o indica qué cambiar."

**No avanzar sin un "ok" explícito** (o equivalente afirmativo).

---

### FASE 2 — Investigación autónoma via MCP

Esta fase es **mayormente autónoma** — el usuario no necesita intervenir
a menos que haya problemas. Muestra progreso periódicamente.

#### 2a. Preparar el notebook

```
1. notebook_list → buscar si ya existe un notebook sobre el tema
   - Si existe: preguntar si reutilizar o crear nuevo
   - Si no existe: notebook_create(title="Investigación: {NOMBRE_PROYECTO}")
2. Guardar notebook_id para todas las operaciones siguientes
```

#### 2b. Inyectar contexto del usuario

Si el usuario proporcionó contexto específico (documentos, URLs, texto):
```
- notebook_add_text(contenido del brief como fuente de contexto)
- notebook_add_url(URLs mencionadas por el usuario)
- notebook_add_local_file(archivos locales si los proporcionó)
- notebook_add_drive(docs de Drive si aplica)
```

#### 2c. Investigación web automatizada

Ejecutar búsquedas en rondas, de lo general a lo específico:

**Ronda 1 — Mapeo general:**
```
research_start(
  notebook_id, 
  query="{tema principal} overview",
  source="web",
  mode="fast"
)
→ research_poll(notebook_id)  # esperar completación
→ research_import(notebook_id, task_id, sources)  # importar TODAS
```

**Ronda 2 — Profundización por subtema:**
Para cada subtema identificado en la Fase 1:
```
research_start(
  notebook_id,
  query="{subtema específico}",
  source="web",
  mode="fast"  # usar "deep" solo si el usuario pidió investigación exhaustiva
)
→ research_poll → research_import
```

**Ronda 3 — Preguntas específicas** (si el nivel del usuario es ≥5):
```
research_start(
  notebook_id,
  query="{pregunta técnica o de nicho del usuario}",
  source="web",
  mode="deep"
)
→ research_poll → research_import
```

**Criterios de parada:**
- Máximo **5 rondas** de research para evitar saturación.
- Si una ronda devuelve <3 fuentes nuevas, detener esa línea de investigación.
- Si el total de fuentes supera 40, detener y pasar a análisis.

#### 2d. Manejo de errores MCP

```
Si una llamada MCP falla:
  1. Log interno del error exacto
  2. Reintentar 1 vez con parámetros simplificados
  3. Si falla de nuevo:
     - Si es auth: ejecutar refresh_auth y reintentar
     - Si es rate limit: esperar 30s y reintentar
     - Si es otro error: notificar al usuario con el error,
       proponer alternativa, y continuar con fuentes disponibles
  4. NUNCA detener todo el proceso por un solo error
```

#### 2e. Análisis cruzado (notebook_query)

Una vez cargadas las fuentes, interrogar el notebook para extraer insights:

```
notebook_query(notebook_id, "¿Cuáles son los hallazgos principales sobre {tema}?")
notebook_query(notebook_id, "¿Qué contradicciones o debates existen entre las fuentes?")
notebook_query(notebook_id, "¿Qué vacíos de información detectas sobre {subtemas}?")
notebook_query(notebook_id, "Resume las fuentes más relevantes y por qué son importantes")
```

Guardar las respuestas — estas alimentan los entregables de la Fase 3.

**Notificar progreso al usuario:**
> "✅ Investigación completada. Encontré N fuentes de M búsquedas.
> Ahora genero los entregables. Esto toma 1–3 minutos."

---

### FASE 3 — Generación de entregables

#### 3a. Entregables MCP nativos (si fueron solicitados)

Ejecutar en paralelo los que apliquen:

```
# Si pidió mapa mental:
mind_map_generate(source_ids=[...])
→ mind_map_save(notebook_id, mind_map_json, source_ids, title="Mapa: {tema}")

# Si pidió audio:
audio_overview_create(notebook_id, language="es", focus_prompt="{resumen del brief}")
→ studio_poll(notebook_id)  # esperar completación

# Si pidió video:
video_overview_create(notebook_id, language="es", focus_prompt="{resumen del brief}")
→ studio_poll(notebook_id)

# Si pidió slides:
slide_deck_create(notebook_id, language="es", focus_prompt="{resumen del brief}")
→ studio_poll(notebook_id)

# Si pidió flashcards:
flashcards_create(notebook_id, language="es", focus_prompt="{resumen del brief}")
→ studio_poll(notebook_id)

# Si pidió infografía:
infographic_create(notebook_id, language="es", focus_prompt="{resumen del brief}")
→ studio_poll(notebook_id)

# Si pidió tabla comparativa:
data_table_create(notebook_id, language="es", focus_prompt="{resumen del brief}")
→ studio_poll(notebook_id)

# Reporte (siempre):
report_create(notebook_id, language="es", focus_prompt="{resumen del brief}")
→ studio_poll(notebook_id)
```

#### 3b. Entregables Markdown locales (siempre se generan)

Usando la información de `notebook_query` y `report_create`, generar:

| Archivo                  | Contenido | Largo objetivo |
|--------------------------|-----------|----------------|
| `resumen_ejecutivo.md`   | Contexto, hallazgos clave, conclusión, próximos pasos | 500–800 palabras |
| `informe_detallado.md`   | Secciones por subtema con citas inline `[Fuente N]` | 1500–3000 palabras |
| `mapa_mental.md`         | Diagrama Mermaid jerárquico (mindmap o flowchart) | N/A |
| `fuentes.md`             | Tabla indexada: #, título, URL, fecha, resumen 2 líneas, relevancia (🟢Alta/🟡Media/🔴Baja) | Todas las fuentes |
| `preguntas_abiertas.md`  | Vacíos, hipótesis no confirmadas, sugerencias de profundización | 5–15 preguntas |

**Formato de cabecera para TODOS los archivos:**

```markdown
# [Título del entregable]

> 📅 Generado: YYYY-MM-DD  
> 🔬 Tema: [nombre del proyecto]  
> 📚 Fuentes usadas: N  
> 🤖 Generado con: NotebookLM MCP + Antigravity
```

**Reglas de citación:**
- Toda afirmación factual → `[Fuente N]` referenciando `fuentes.md`
- Si un párrafo tiene <2 fuentes de respaldo → marcar con `⚠️ POCO RESPALDADO`
- No inventar ni inferir datos que no estén en las fuentes importadas
- Si hay contradicciones entre fuentes, documentarlas explícitamente

**Mapa mental Mermaid** — usar esta estructura:

```markdown
```mermaid
mindmap
  root(("{TEMA PRINCIPAL}"))
    subtema_1["Subtema 1"]
      hallazgo_1a["Hallazgo A"]
      hallazgo_1b["Hallazgo B"]
    subtema_2["Subtema 2"]
      hallazgo_2a["Hallazgo C"]
      hallazgo_2b["Hallazgo D"]
    preguntas_abiertas["❓ Preguntas abiertas"]
      pregunta_1["Pregunta 1"]
      pregunta_2["Pregunta 2"]
`` `
```

---

### FASE 4 — Almacenamiento y entrega

#### 4a. Crear estructura local

```
{CWD}/INVESTIGACION_{NOMBRE_PROYECTO}/
├── resumen_ejecutivo.md
├── informe_detallado.md
├── mapa_mental.md
├── fuentes.md
├── preguntas_abiertas.md
└── README.md              ← índice con links a cada archivo
```

**Reglas de nombrado:**
- `{NOMBRE_PROYECTO}` = nombre corto del brief, en `MAYUSCULAS_CON_GUIONES_BAJOS`
- Sin espacios, acentos, eñes ni caracteres especiales
- Máximo 40 caracteres

#### 4b. README.md de índice

```markdown
# 📚 Investigación: {Tema}

> Generado: YYYY-MM-DD | Fuentes: N | Notebook ID: {id}

## Archivos

| Archivo | Descripción |
|---------|-------------|
| [resumen_ejecutivo.md](./resumen_ejecutivo.md) | Resumen de 500-800 palabras |
| [informe_detallado.md](./informe_detallado.md) | Informe completo con citas |
| [mapa_mental.md](./mapa_mental.md) | Diagrama Mermaid del tema |
| [fuentes.md](./fuentes.md) | Tabla de todas las fuentes |
| [preguntas_abiertas.md](./preguntas_abiertas.md) | Vacíos y próximos pasos |

## Artefactos NotebookLM

_(listar aquí los artefactos generados: audio, video, slides, etc. con estado)_

## Para continuar investigando

El notebook sigue activo en NotebookLM con ID `{notebook_id}`.
Puedes pedir: "profundiza en [subtema]" o "agrega fuentes sobre [nuevo ángulo]".
```

#### 4c. Entrega final

Mostrar al usuario:

```
✅ Investigación completada

📁 Carpeta: {ruta_absoluta}/INVESTIGACION_{NOMBRE_PROYECTO}/
📄 Archivos generados: {N}
📚 Fuentes recopiladas: {N}
🧠 Notebook activo: {notebook_id}

{Árbol de archivos con tamaños}

¿Quieres que profundice en algún subtema, genere un audio/video overview,
o exporte algo en otro formato?
```

---

## Constraints

### Obligatorias
- Responder siempre en el **idioma del usuario** (detectar automáticamente)
- **No avanzar de Fase 1 a Fase 2 sin confirmación explícita** del brief
- Fases 2–4 son autónomas — no pedir confirmación intermedia salvo errores
- No inventar ni inferir información que no esté en las fuentes
- Nunca mostrar claves API, tokens ni datos sensibles en archivos generados
- Citar TODA afirmación factual con `[Fuente N]`

### De calidad
- Priorizar fuentes de los últimos 2 años salvo indicación contraria
- Si un subtema tiene <2 fuentes: marcar `⚠️ POCO RESPALDADO`
- Si hay contradicciones entre fuentes: documentar ambas posiciones
- Prefiere fuentes primarias (papers, docs oficiales) sobre secundarias (blogs, foros)
- Incluir la fecha de la fuente siempre que sea posible

### Técnicas
- Máximo 5 rondas de `research_start` por investigación
- Máximo 40 fuentes por notebook (evitar saturación de contexto)
- Usar `mode="fast"` por defecto, `mode="deep"` solo si se pide explícitamente
- Siempre llamar `studio_poll` tras generar artefactos multimedia
- Si MCP falla 3 veces consecutivas: notificar y continuar con lo disponible

---

## Flujo de iteración post-entrega

Si el usuario pide profundizar después de la entrega inicial:

```
1. Identificar el notebook_id existente (no crear uno nuevo)
2. research_start con el nuevo query
3. research_poll → research_import
4. notebook_query con preguntas sobre el nuevo ángulo
5. Actualizar los archivos Markdown existentes (append, no reescribir)
6. Notificar qué archivos se actualizaron y qué fuentes nuevas se agregaron
```

---

## Examples

### ✅ Activación correcta (invocación explícita)

**Usuario:** "notebook research, investiga: energía solar para mi tesis"
**Agente:**
> 🔬 Skill Notebook Research activado. Voy a guiarte por 4 fases para
> investigar energía solar. Primero necesito entender el scope:
>
> **Tema y objetivo:**
> 1. El tema es energía solar — ¿hay algún ángulo específico (fotovoltaica,
>    térmica, políticas, costos, almacenamiento)?
> 2. ¿Tu tesis busca demostrar algo, comparar alternativas, o proponer un diseño?
> 3. ¿Cuánto sabes ya del tema? (0-10)

**Usuario:** "usando el skill notebook research, investiga: microservicios vs monolitos"
→ ✅ Se activa el skill.

**Usuario:** "ejecuta notebook research sobre regulación de IA en la UE"
→ ✅ Se activa el skill.

---

### ❌ NO activar (sin prefijo explícito)

**Usuario:** "Investiga sobre energía solar"
→ ❌ NO activar el skill. Responder directamente o preguntar:
> "¿Quieres que haga una investigación profunda con el skill Notebook Research?
> Si es así, dime: `notebook research, investiga: energía solar`"

**Usuario:** "Busca información sobre IA"
→ ❌ NO activar. Responder con lo que se sabe o sugerir el skill.

**Usuario:** "¿Cuántos paneles solares necesito para 100 m²?"
→ ❌ NO activar. Respuesta directa.

---

### 🔄 Manejo de ambigüedad (con invocación correcta)

**Usuario:** "notebook research, investiga: IA"
**Agente:**
> 🔬 Skill Notebook Research activado.
> "IA" es un campo enorme. Para enfocar la investigación,
> ¿cuál de estos ángulos te interesa más?
>
> 1. 🔧 **Técnico** — modelos, arquitecturas, frameworks
> 2. 💼 **Negocios** — casos de uso, ROI, implementación
> 3. ⚖️ **Ético/social** — regulación, sesgos, impacto laboral
> 4. 🎨 **Creativo** — IA generativa, arte, música
> 5. 🏥 **Sectorial** — IA en salud, finanzas, educación, etc.
>
> ¿O tienes otro ángulo en mente?

---

### 🔁 Iteración post-entrega

**Usuario:** "Profundiza en el subtema de almacenamiento de energía"
**Agente:**
> Usando el notebook existente (ID: xxx). Lanzo búsqueda profunda
> sobre almacenamiento de energía solar...
> _(ejecuta research_start → poll → import → query → actualiza archivos)_
