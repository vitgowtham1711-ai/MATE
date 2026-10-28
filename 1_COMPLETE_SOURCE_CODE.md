# 📦 Deliverable 1: Complete Source Code Manifest & Codebase Architecture

**Project:** MATE — Intelligent Multi-Platform AI Co-Pilot  
**Team:** Alpha And Beta  
**Lead:** Gowtham Datta Sai V | **Member:** Sai Teja  
**Date:** October 2026  
**Evaluation:** 48-Hour Hackathon  

---

## 1. Executive Summary

**MATE** (Multi-platform Autonomous Task Executive) is a production-grade, multi-platform AI co-pilot designed to unify fragmented enterprise workflows. Unlike simple wrapper chatbots or raw single-turn CLI tool callers, MATE introduces an intelligent cognitive orchestration layer that links **Google Workspace** (Gmail, Drive, Docs, Sheets, Calendar), **GitHub**, **YouTube**, and the **Local Desktop Filesystem** using the open **Model Context Protocol (MCP)**.

All operations are executed through an intuitive, luxury glassmorphic Web Dashboard with built-in human-in-the-loop (HITL) safety confirmations, proactive suggestions, and visual strategic planning cards.

---

## 2. Codebase Directory Tree

```
c:/MATE/
├── .env                              # Environment configuration & API credentials
├── .gitignore                        # Git exclusion rules
├── credentials.json                  # Google Cloud OAuth Client Credentials
├── pyproject.toml                    # UV / PEP 621 package metadata & dependencies
├── uv.lock                           # Pinned deterministic dependency lockfile
├── start_ui.bat                      # 1-Click Windows execution script for judges
│
├── agent.py                          # Core CrewAI Agent & Multi-MCP Adapter Engine (513 lines)
├── mate_brain.py                     # Cognitive Brain: IntentRouter, Planner, Suggester, Memory (378 lines)
├── server.py                         # FastAPI Web API Gateway & Async MCP Manager (473 lines)
├── ui.py                             # Rich Terminal Interface & TUI Visualizer (338 lines)
├── generate_ppt.py                   # Automated PowerPoint Presentation Generator (655 lines)
├── test_mate.py                      # Integration validation & mock test suite (84 lines)
│
├── web/                              # Modern Obsidian Glassmorphic Web Dashboard
│   ├── index.html                    # Dashboard markup, tool explorer, plan cards (10.7 KB)
│   ├── styles.css                    # Obsidian glassmorphic styling & micro-animations (24.4 KB)
│   ├── app.js                        # Reactive UI state, REST API client, safety modal (19.5 KB)
│   └── architecture.html             # Interactive Mermaid system architecture visualizer (15.5 KB)
│
└── docs/                             # Official 48-Hour Hackathon Deliverables Hub
    ├── HACKATHON_DELIVERABLES_INDEX.md # Master Submission & Evaluation Index
    ├── 1_COMPLETE_SOURCE_CODE.md     # This document: Source code manifest & breakdown
    ├── 2_BRD_PROJECT_DOCUMENTATION.md# Business Requirements Document & Technical Specs
    ├── 3_ARCHITECTURE_DIAGRAM.md     # Multi-tier System & Cognitive Sequence Diagrams
    ├── 4_INSTALLATION_GUIDE.md       # Step-by-step setup, prerequisites & runbook
    ├── 5_FEATURE_LIST.md             # Complete directory of all 165+ MCP tools & capabilities
    ├── 6_BUSINESS_USE_CASES.md       # Enterprise scenarios, workflows & ROI analysis
    ├── 7_FUTURE_ROADMAP.md           # Product evolution roadmap (v1.2 to v3.0)
    └── architecture.html             # Standalone interactive architecture diagram
```

---

## 3. Module Breakdown & Technical Responsibilities

### 3.1 `agent.py` — Core Orchestration Engine
- **Role:** Central agent definition, tool aggregation, and execution runner.
- **Key Classes / Functions:**
  - `make_agent(tools)`: Instantiates a `CrewAI Agent` with the `gemini-2.5-flash` model, passing comprehensive backstory instructions, strict anti-hallucination rules, and platform-specific execution parameters (e.g. `user_google_email`).
  - `run_command(agent, command, memory, plan, doc_context)`: Executes a synchronous CrewAI task while injecting session history, decomposition plans, and document contexts.
  - `get_desktop_path()`: Cross-platform resolver that auto-detects the active Windows Desktop (including OneDrive redirected Desktops via Windows Registry query `User Shell Folders`).
  - `StdioServerParameters`: Configures MCP client parameters for Google Workspace (`workspace-mcp`), GitHub (`@modelcontextprotocol/server-github`), YouTube (`mcp-youtube`), and Local Filesystem (`@modelcontextprotocol/server-filesystem`).

### 3.2 `mate_brain.py` — Cognitive Layer
- **Role:** Elevates MATE from a reactive command-line tool into an intelligent executive thinker.
- **Key Components:**
  - `SessionMemory`: Maintains a rolling window of conversational turns, pending contextual suggestions, and user intent state.
  - `IntentRouter`: High-speed regex and semantic pattern classifier that routes user queries into 4 discrete modes:
    1. **`plan`**: Multi-step, complex workflows requiring visual decomposition and risk analysis before tool invocation.
    2. **`chat`**: Advisory questions, brainstorming, explanations, or casual conversation without unnecessary tool polling.
    3. **`execute`**: Direct single-turn or targeted multi-platform operational commands.
    4. **`suggestion`**: Single-character shortcut (`1`, `2`, `3`) that activates proactive recommendations.
  - `MATEPlanner`: Deconstructs complex user prompts into discrete, sequentially ordered execution steps, calculating risk levels (`Low 🟢`, `Medium 🟡`, `High 🔴`).
  - `SuggestionEngine`: Heuristic and contextual recommendation engine that proposes the logical next 3 actions after every task completion.
  - `AdvisorAgent`: Lightweight conversational agent for immediate advisory and strategic consultation.

### 3.3 `server.py` — FastAPI Web API Gateway
- **Role:** Provides high-throughput asynchronous REST endpoints and persistent MCP connection management for the Web UI.
- **Key Endpoints:**
  - `GET /api/status`: Real-time health check reporting connection status of Google Workspace, GitHub, YouTube, and Desktop, along with tool counts.
  - `GET /api/tools`: Enumerates all 165+ connected tools, their schemas, argument specifications, and source platforms.
  - `POST /api/chat`: Primary conversational endpoint. Parses queries, invokes IntentRouter, handles planning cards, triggers HITL safety checks, and executes CrewAI tasks.
  - `POST /api/confirm`: Human-in-the-loop (HITL) approval endpoint to proceed with destructive or modifying operations.
  - `POST /api/upload`: Multi-part document upload endpoint enabling local document context ingestion into the LLM context.
  - `POST /api/clear`: Flushes session memory and active context.
- **Lifecycle Management:** Utilizes Python's `asynccontextmanager` and `contextlib.ExitStack` to keep `StdioServerParameters` sub-processes active and responsive across all incoming web requests.

### 3.4 `web/` — Obsidian Glassmorphic Web Dashboard
- **`index.html`**: Clean semantic HTML5 layout with dark obsidian glassmorphism, responsive tool cards, execution modals, and real-time status orbs.
- **`styles.css`**: Pure modern Vanilla CSS with CSS variable tokens, blur effects (`backdrop-filter: blur(16px)`), micro-interactions, responsive flex/grid layouts, and zero heavy external CSS frameworks.
- **`app.js`**: Vanilla JS client with reactive state, Markdown-to-HTML rendering, interactive tool search filter, 1-click suggestion triggers, and HITL safety modal handlers.
- **`architecture.html`**: Dedicated interactive visual page with Mermaid.js architecture diagrams, component cards, and sequence charts.

### 3.5 `ui.py` — Rich Terminal Interface
- **Role:** Terminal User Interface (TUI) powered by `rich`, featuring styled banners, live progress spinners, side-by-side tool inspector tables, and colorful markdown streams.

### 3.6 `generate_ppt.py` — Presentation Suite
- **Role:** Autonomous script utilizing `python-pptx` to generate a 16:9 widescreen, dark-themed, 15-slide presentation (`MATE_Presentation.pptx`) detailing project architecture, benchmarks, ROI, and roadmap for judges.

---

## 4. Key Execution Entry Points

### 4.1 Launching the Web Interface (Recommended)
```powershell
# Option A: Double click start_ui.bat
./start_ui.bat

# Option B: Run via uv
uv run python server.py
```
*Access via browser:* `http://localhost:8000`

### 4.2 Launching the Terminal Interface (CLI Mode)
```powershell
uv run python agent.py
```

### 4.3 Generating the Official Hackathon Pitch Deck
```powershell
uv run python generate_ppt.py
```

---

## 5. Dependency Manifest (`pyproject.toml`)

```toml
[project]
name = "mate"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "crewai-tools[mcp]>=1.15.30",
    "crewai[google-genai]>=1.15.30",
    "fastapi>=0.162.0",
    "pymysql>=1.2.3",
    "python-dotenv>=1.2.4",
]
```

---

## 6. Verification & Quality Assurance

- **Static Analysis:** Strict typing across `mate_brain.py` and `server.py` using Pydantic models.
- **Deterministic Dependencies:** Pinned through `uv.lock`.
- **Zero Hallucination Policy:** Prompt templates explicitly prohibit hallucinated IDs, enforce strict tool schema validation, and require actual API outputs.
- **Error Resiliency:** Multi-level fallback handles missing tokens gracefully without crashing the server or preventing other platforms from operating.
