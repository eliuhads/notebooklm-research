# 🔬 NotebookLM Auto-Research Skill

> An autonomous research skill for [Google Gemini CLI](https://github.com/google-gemini/gemini-cli) / Antigravity that leverages **NotebookLM MCP** to transform any topic into a fully documented research project.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![MCP](https://img.shields.io/badge/MCP-NotebookLM-4285F4?logo=google)](https://notebooklm.google.com)

---

## ✨ What it does

Given a research topic, this skill autonomously:

1. **Intake** — Gathers scope through structured questions (topic, objective, depth, output format)
2. **Research** — Runs multi-round web searches via NotebookLM MCP (`research_start` → `research_poll` → `research_import`)
3. **Analyze** — Cross-queries imported sources to extract insights, contradictions, and gaps
4. **Deliver** — Generates structured Markdown deliverables + optional multimedia outputs

### 📦 Output files

```
INVESTIGACION_{PROJECT_NAME}/
├── README.md               ← Index with links to all files
├── resumen_ejecutivo.md     ← 500-800 word executive summary
├── informe_detallado.md     ← Full report with inline citations [Source N]
├── mapa_mental.md           ← Mermaid mindmap diagram
├── fuentes.md               ← Source table: URL, date, summary, relevance
└── preguntas_abiertas.md    ← Knowledge gaps & next steps
```

### 🎙️ Optional multimedia outputs (via NotebookLM)

- Audio Overview (podcast-style)
- Video Overview
- Slide Deck
- Flashcards
- Infographic
- Comparative Data Table

---

## 🚀 Installation

### For Gemini CLI / Antigravity

Copy the skill to your skills directory:

```bash
mkdir -p ~/.gemini/antigravity/skills/auto-research-notebooklm
cp SKILL.md ~/.gemini/antigravity/skills/auto-research-notebooklm/
```

### Prerequisites

- **NotebookLM MCP server** must be configured and authenticated
- A Google account with access to [NotebookLM](https://notebooklm.google.com)

---

## 💬 Usage

Just ask naturally — the skill activates on research-intent signals:

```
"Investiga sobre energía solar para mi tesis"
"Hazme un deep-dive sobre microservicios vs monolitos"
"Prepárame un briefing sobre regulación de IA en la UE"
"Busca información sobre técnicas de prompt engineering"
```

### Won't activate for:
```
"¿Cuánto pesa el sol?"           → Direct answer
"Traduce esto al inglés"         → Not research
"¿Cuántos paneles solares necesito?" → Single calculation
```

**Decision rule**: If the answer requires ≥3 sources to be rigorous, the skill activates. Otherwise, it responds directly.

---

## ⚙️ MCP Tools Used

| Tool | Purpose |
|------|---------|
| `notebook_create` | Create dedicated research notebook |
| `notebook_add_url/text/local_file` | Add user-provided sources |
| `research_start` | Launch web/Drive searches |
| `research_poll` → `research_import` | Monitor and import discovered sources |
| `notebook_query` | Extract insights from loaded sources |
| `report_create` | Generate structured report |
| `mind_map_generate` + `mind_map_save` | Create visual mind maps |
| `audio/video_overview_create` | Generate multimedia summaries |
| `slide_deck_create` | Generate presentations |
| `flashcards_create` | Generate study cards |

---

## 📄 License

MIT — use it, fork it, improve it.

---

## 🤝 Contributing

PRs welcome! If you improve the research workflow, add new output formats, or fix edge cases, feel free to submit.

---

*Built with ❤️ by [@eliuhads](https://github.com/eliuhads) using Antigravity + NotebookLM MCP*
