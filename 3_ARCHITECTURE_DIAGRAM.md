# 🏛️ Deliverable 3: Architecture Diagram & Technical Design

**Project:** MATE — Intelligent Multi-Platform AI Co-Pilot  
**Team:** Alpha And Beta  
**Lead:** Gowtham Datta Sai V | **Member:** Sai Teja  
**Date:** October 2026  
**Interactive Visualizer:** [Launch Interactive Architecture Dashboard](file:///c:/MATE/docs/architecture.html)

---

## 1. High-Level Multi-Tier System Architecture

MATE operates on a decoupled, 5-tier architecture that separates the user interface, API gateway, cognitive orchestration brain, autonomous agents, and underlying Model Context Protocol (MCP) servers.

```mermaid
graph TB
    subgraph Tier1["1. Presentation Tier (Web & Terminal)"]
        UI_Web["Obsidian Glassmorphic Web UI<br/>(http://localhost:8000)"]
        UI_TUI["Rich Terminal Interface<br/>(agent.py / ui.py)"]
        UI_Arch["Interactive Architecture Viewer<br/>(architecture.html)"]
    end

    subgraph Tier2["2. API Gateway & Lifespan Controller (server.py)"]
        API_FastAPI["FastAPI ASGI Gateway<br/>(CORS, Static Files, REST API)"]
        API_Lifespan["Async Lifespan & ExitStack<br/>(Persistent Stdio Subprocesses)"]
        API_HITL["Human-in-the-Loop Interceptor<br/>(/api/confirm)"]
    end

    subgraph Tier3["3. Cognitive Brain Layer (mate_brain.py)"]
        BR_Router["IntentRouter<br/>(Regex & Semantic Classification)"]
        BR_Memory["SessionMemory<br/>(Rolling Window 15-turn History)"]
        BR_Planner["MATEPlanner<br/>(Task Decomposition & Risk Scoring)"]
        BR_Suggester["SuggestionEngine<br/>(Proactive 1-Click Triggers)"]
        BR_Advisor["AdvisorAgent<br/>(Strategic Advisory Mode)"]
    end

    subgraph Tier4["4. Agent Orchestration Tier (agent.py)"]
        AG_Crew["CrewAI Orchestrator<br/>(Sequential Process & Anti-Hallucination)"]
        AG_LLM["Google Gemini 2.5 Flash<br/>(Multimodal LLM / 1M Context)"]
        AG_Adapters["MCPServerAdapter Registry<br/>(165+ Unified Tool Signatures)"]
    end

    subgraph Tier5["5. Multi-Platform MCP Subprocesses & External Services"]
        MCP_Google["Google Workspace MCP<br/>(workspace-mcp via uvx)<br/>119 Tools"]
        MCP_GitHub["GitHub MCP<br/>(@modelcontextprotocol/server-github via npx)<br/>26 Tools"]
        MCP_YouTube["YouTube MCP<br/>(mcp-youtube via uvx)<br/>6 Tools"]
        MCP_Desktop["Local Filesystem MCP<br/>(@modelcontextprotocol/server-filesystem)<br/>14 Tools"]
        
        EXT_Google["Google Cloud APIs<br/>(Gmail, Drive, Docs, Calendar)"]
        EXT_GitHub["GitHub REST / GraphQL API"]
        EXT_YouTube["YouTube Data v3 API"]
        EXT_OS["Windows Desktop OS<br/>(C:\Users\...\Desktop)"]
    end

    UI_Web -->|HTTP / JSON REST| API_FastAPI
    UI_TUI -->|Direct Function Calls| Tier3
    API_FastAPI --> API_Lifespan
    API_FastAPI --> API_HITL
    API_FastAPI --> BR_Router
    
    BR_Router -->|Plan Mode| BR_Planner
    BR_Router -->|Chat Mode| BR_Advisor
    BR_Router -->|Execute Mode| AG_Crew
    BR_Router -->|Suggestion Mode| BR_Suggester
    
    BR_Planner --> AG_Crew
    AG_Crew --> AG_LLM
    AG_Crew --> AG_Adapters
    
    AG_Adapters -->|JSON-RPC 2.0 / Stdio| MCP_Google
    AG_Adapters -->|JSON-RPC 2.0 / Stdio| MCP_GitHub
    AG_Adapters -->|JSON-RPC 2.0 / Stdio| MCP_YouTube
    AG_Adapters -->|JSON-RPC 2.0 / Stdio| MCP_Desktop

    MCP_Google --> EXT_Google
    MCP_GitHub --> EXT_GitHub
    MCP_YouTube --> EXT_YouTube
    MCP_Desktop --> EXT_OS
```

---

## 2. Cognitive Flow & Intent Routing Architecture

The `IntentRouter` analyzes raw user prompts before invoking costly LLM tool-calling loops. This reduces token waste, lowers latency, and ensures high safety for mutating operations.

```mermaid
flowchart TD
    Start(["User Submits Query"]) --> CheckShortcut{"Is Query a Single Digit<br/>('1', '2', '3')?"}
    
    CheckShortcut -- Yes --> TriggerSuggestion["SuggestionEngine:<br/>Fetch Pending Action from SessionMemory"]
    TriggerSuggestion --> RouteExecute
    
    CheckShortcut -- No --> CheckComplex{"Is Query Multi-Step?<br/>('and then', 'after that', 'organize')"}
    
    CheckComplex -- Yes --> PlanGen["MATEPlanner:<br/>1. Decompose into Atomic Steps<br/>2. Assign Risk (🟢 Low / 🟡 Med / 🔴 High)<br/>3. Return Plan Card to Web UI"]
    PlanGen --> AwaitPlanConfirm{"User Approves Plan?"}
    AwaitPlanConfirm -- Yes --> RouteExecute["Route to CrewAI Execution"]
    AwaitPlanConfirm -- No / Edit --> RevisePlan["Revise Plan or Abort"]
    
    CheckComplex -- No --> CheckChat{"Is Query Advisory / Brainstorming?<br/>('how to', 'explain', 'ideas')"}
    CheckChat -- Yes --> ChatGen["AdvisorAgent:<br/>Direct Gemini Response<br/>(No MCP tool overhead)"]
    ChatGen --> FormatResponse
    
    CheckChat -- No --> CheckSafety{"Contains Destructive Keywords?<br/>(send, delete, remove, merge, trash)"}
    CheckSafety -- Yes --> SafetyIntercept["HITL Safety Interceptor:<br/>1. Hold Execution in State<br/>2. Dispatch Safety Modal to Web UI"]
    SafetyIntercept --> AwaitUserApproval{"User Confirms Execution?"}
    AwaitUserApproval -- Yes --> RouteExecute
    AwaitUserApproval -- No --> AbortExecution["Abort Action Gracefully"]
    
    CheckSafety -- No --> RouteExecute
    
    RouteExecute --> ExecCrew["CrewAI Agent executes tools<br/>across Google, GitHub, YouTube & Desktop"]
    ExecCrew --> SessionStore["SessionMemory:<br/>Record prompt, output & tool summary"]
    SessionStore --> Suggester["SuggestionEngine:<br/>Generate 3 contextual next-step triggers"]
    Suggester --> FormatResponse(["Deliver Structured Response to User"])
```

---

## 3. End-to-End Sequence Diagram (Cross-Platform Execution with HITL)

The sequence diagram below illustrates a cross-platform command: **"Summarize unread GitHub issues and email the briefing to the team."**

```mermaid
sequenceDiagram
    autonumber
    actor User as Knowledge Worker
    participant Web as Web Dashboard (app.js)
    participant Server as FastAPI Server (server.py)
    participant Brain as Cognitive Brain (mate_brain.py)
    participant Agent as CrewAI & Gemini (agent.py)
    participant GH as GitHub MCP Server
    participant Google as Google Workspace MCP Server

    User->>Web: "Summarize top open issues on 'my-org/repo' and draft email to team"
    Web->>Server: POST /api/chat { prompt, mode: "auto" }
    
    Server->>Brain: IntentRouter.classify(prompt)
    Brain-->>Server: Intent: PLAN (Multi-step, High Risk)
    
    Server->>Brain: MATEPlanner.generate_plan(prompt)
    Brain-->>Server: Plan: [1. List issues, 2. Synthesize summary, 3. Send email] (Risk: 🔴 High)
    Server-->>Web: Return Plan Card & Await Confirmation
    
    User->>Web: Clicks "Approve Plan"
    Web->>Server: POST /api/chat { prompt, confirmed_plan: true }
    
    Server->>Agent: Run Task with SessionMemory & Tool Registry
    Agent->>GH: Call tool: list_issues(owner="my-org", repo="repo", state="open")
    GH-->>Agent: Returns 5 active issue payloads
    
    Agent->>Agent: Gemini 2.5 Flash synthesizes executive markdown briefing
    
    Agent->>Google: Pre-call tool: send_gmail_message(to="team@company.com", body=...)
    Server->>Server: Safety Guardrail Interceptor detects action keyword 'send'
    Server-->>Web: Trigger Safety Confirmation Modal { action: "Send Gmail", recipient: "team@company.com" }
    
    User->>Web: Clicks "Authorize & Send"
    Web->>Server: POST /api/confirm { action_id: "act_48291" }
    
    Server->>Google: Dispatches tool call: send_gmail_message(...)
    Google-->>Agent: Success: Message sent (ID: #msg_81920)
    
    Agent-->>Server: Task Completed with structured summary
    Server->>Brain: SuggestionEngine.generate(result)
    Brain-->>Server: Proactive Suggestions: [1. Create Calendar Event, 2. Save to Drive, 3. File Desktop Note]
    Server-->>Web: HTTP 200 OK: Final Briefing + 1-Click Suggestion Badges
    Web-->>User: Displays glassmorphic response card with real-time status
```

---

## 4. MCP Subprocess Lifecycle & Process Management

MATE uses Python's asynchronous `contextlib.ExitStack` within FastAPI's `lifespan` hook to manage external processes:

| Component | Execution Command | Transport | Communication Standard |
| :--- | :--- | :--- | :--- |
| **Google Workspace** | `uvx --from workspace-mcp workspace-mcp --single-user --tool-tier complete` | Standard I/O (Stdio) | JSON-RPC 2.0 |
| **GitHub** | `npx -y @modelcontextprotocol/server-github` | Standard I/O (Stdio) | JSON-RPC 2.0 |
| **YouTube** | `uvx --with mcp<2 --from mcp-youtube mcp-youtube` | Standard I/O (Stdio) | JSON-RPC 2.0 |
| **Desktop Filesystem**| `npx -y @modelcontextprotocol/server-filesystem <DESKTOP_DIR>` | Standard I/O (Stdio) | JSON-RPC 2.0 |

### Lifecycle Resilience Features:
1. **Zero Cold-Start Tool Invocations:** MCP subprocesses are spawned once at server startup and kept alive, reducing tool-call latency from seconds to milliseconds.
2. **Graceful Degradation:** If any MCP server fails during initialization (e.g. invalid GitHub token), the remaining servers initialize successfully. The UI displays an amber warning status rather than crashing.
3. **Clean Shutdown:** On server termination (`SIGINT` / `SIGTERM`), the `ExitStack` cleanly closes standard input/output pipes and terminates child processes without leaving orphaned background processes.

---

## 5. Security & Isolation Matrix

```
┌────────────────────────────────────────────────────────┐
│                   Untrusted Network                    │
└───────────────────────────┬────────────────────────────┘
                            │ HTTPS / Localhost
┌───────────────────────────▼────────────────────────────┐
│          MATE Security & Sandbox Boundary              │
│                                                        │
│  [1] Credential Security:                              │
│      - Tokens stored strictly in local .env & OS disk  │
│      - No external telemetry or cloud sync             │
│                                                        │
│  [2] Execution Sandboxing:                             │
│      - Local Filesystem scoped strictly to Desktop     │
│      - Restrictive path resolution prevents /etc/ or   │
│        system directory traversal                      │
│                                                        │
│  [3] Human-in-the-Loop Interceptor:                   │
│      - Zero automated execution of destructive tools   │
│      - Explicit user token approval per action         │
└────────────────────────────────────────────────────────┘
```
