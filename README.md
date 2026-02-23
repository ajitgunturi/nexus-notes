# Project Nexus — Study Guide

A self-contained software engineering curriculum built around one project that grows with you. You start with a 20-line Python script and end with a distributed, AI-powered autonomous agent — using the same codebase the entire way.

**65 days · ~3 hours per session · ~195 hours of deliberate practice**

---

## What Is Nexus?

Nexus is a note manager. That sounds modest. What makes it interesting is what you build *on top of* it across four phases:

| Phase | Nexus Becomes | Skills |
|-------|--------------|--------|
| **1** — Days 1–18 | A CLI tool you can actually use | Python, Git, File I/O, OOP, Testing |
| **2** — Days 19–38 | A REST API any app can talk to | HTTP, SQL, FastAPI, Docker, JWT Auth |
| **3** — Days 39–52 | An AI that understands your notes | Embeddings, Vector Search, RAG, LLMs |
| **4** — Days 53–65 | An agent that acts on your behalf | LangGraph, Celery, Queues, Deployment |

The philosophy: real engineers don't rewrite from scratch every week. They evolve a codebase. Your Git history will tell the story of that evolution — and it becomes a portfolio artifact.

---

## What Is in This Repository

```
docs/
  Nexus_Phase1_CLI_File_Manager.md      ← Days 1–18
  Nexus_Phase2_REST_API_Database.md     ← Days 19–38
  Nexus_Phase3_AI_Brain.md              ← Days 39–52
  Nexus_Phase4_Autonomous_Agent.md      ← Days 53–65

system-instruction.md    ← AI coach prompt (Claude, Gemini, GPT)
SETUP.md                 ← What to install before each phase
README.md                ← You are here
```

Each guide is a complete Markdown file that renders natively on GitHub. Every core concept is explained inline with code examples, right before you need to apply it. Each day follows the same rhythm:

1. **Reading** — theory with inline code examples
2. **Build** — apply the concept to the Nexus project
3. **Review** — a checklist to confirm mastery, then a quiz
4. **Exercises** — three graded exercises (easy / medium / challenge) with hidden solutions
5. **Further Reading** — two curated external links for deeper exploration after the session

Do not skip the quiz. If you get a question wrong, reread the section before moving on. The Further Reading links are optional — the guides are fully usable offline; the links are there when you want to go deeper.

---

## How to Start

### 1. Get your tools ready

Before Day 1, follow [SETUP.md](./SETUP.md) for Phase 1. You only need Python, VS Code, and Git to begin. Later phases introduce more tools — install them when you reach that phase, not all at once.

### 2. Open the Phase 1 guide

Open [`docs/Nexus_Phase1_CLI_File_Manager.md`](./docs/Nexus_Phase1_CLI_File_Manager.md) and start at Day 1. Keep it open while you work in your editor.

### 3. Set up your AI coach (optional but recommended)

The file `system-instruction.md` is a ready-made system prompt that turns any AI assistant into a coaching partner tuned for this curriculum. Paste it into:

- **Claude Code** — add it to your project's `CLAUDE.md`, or paste it as a system prompt
- **Google AI Studio** — paste into the "System instructions" field before starting a chat
- **ChatGPT / OpenAI Playground** — paste into the System message field
- **Any OpenAI-compatible API** — use it as the `system` message

The coach knows all four phases, the Nexus project structure, and how each day's content is organised. It will ask you questions rather than hand you answers — that is intentional.

### 4. Create your Nexus project folder

```bash
mkdir nexus
cd nexus
git init
```

Everything you build goes here. Commit early, commit often. Your goal is 200+ meaningful commits by the end — each one a timestamp of something you learned.

---

## Frequently Asked Questions

**Do I need any prior programming experience?**
No. Phase 1 assumes zero knowledge and builds from "what is a variable" up.

**Can I skip phases?**
Technically yes — each phase's guide is self-contained. But Phase 2 assumes Phase 1 knowledge and Phase 3 assumes Phase 2. Skipping works only if you already have that foundation.

**How strict is the 3-hours-per-day schedule?**
It is a guideline. Some days (especially early in Phase 1) may be done faster. Some days in Phase 3 (installing ML models, debugging async code) may run longer. The day labels are logical units, not calendar deadlines.

**What if I get completely stuck?**
The AI coach exists for exactly this. Describe what you tried, paste the error, and ask for a hint (not the answer). Being stuck and working through it is the most valuable part of the curriculum.

**I finished a phase. What now?**
Each phase ends with a "Concepts to Deepen on Your Own" list. Spend a few days on those before moving to the next phase — they turn surface-level understanding into genuine depth.

---

## Prerequisites Summary

| Phase | Hard Requirements | Recommended |
|-------|------------------|-------------|
| 1 | Python 3.12+, Git, VS Code | — |
| 2 | + Docker Desktop | 8 GB RAM |
| 3 | + OpenAI API key (or Ollama) | 16 GB RAM, SSD |
| 4 | + Redis (via Docker) | 16 GB RAM |

Full installation instructions are in [SETUP.md](./SETUP.md).
