# ⚡ Deliverable 5: Exhaustive Feature List & Capabilities Directory

**Project:** MATE — Intelligent Multi-Platform AI Co-Pilot  
**Team:** Alpha And Beta  
**Lead:** Gowtham Datta Sai V | **Member:** Sai Teja  
**Date:** October 2026  
**Ecosystem Scale:** 165+ Unified MCP Tools across 4 Core Platforms  

---

## 1. High-Level Capabilities Matrix

| Platform / Subsystem | Total Tools | Primary Enterprise Function | Key Protocols / Tech |
| :--- | :---: | :--- | :--- |
| **📧 Google Workspace** | **119 Tools** | Gmail, Google Drive, Google Calendar, Google Docs, Google Sheets | MCP (`workspace-mcp`), OAuth2 |
| **🐙 GitHub** | **26 Tools** | Repositories, Pull Requests, Issues, Commits, Branches, Code Search | MCP (`@modelcontextprotocol/server-github`), REST v3 / GraphQL |
| **▶️ YouTube** | **6 Tools** | Video discovery, transcript extraction, subtitle synthesis, audio streams | MCP (`mcp-youtube`), YouTube Data v3 |
| **💻 Local Desktop** | **14 Tools** | Filesystem exploration, file reading, code editing, directory tree scaffolding | MCP (`@modelcontextprotocol/server-filesystem`), Windows Registry |
| **🧠 Cognitive Brain** | **Engine** | Intent routing, task planning, proactive next steps, multi-turn memory | Python, CrewAI, Gemini 2.5 Flash |
| **🛡️ Safety & HITL** | **Guardrail** | Modifying operation interception, user approval modals, token confirmation | FastAPI, Regex Gate, Pydantic |
| **Total Ecosystem** | **165+ Tools** | Unified single-pane-of-glass executive co-pilot | Glassmorphic Web UI |

---

## 2. Platform-by-Platform Tool Directory

### 2.1 Google Workspace MCP (119 Tools)
Connected via `workspace-mcp` with full tier authorization.

#### A. Gmail Suite
- `gmail_search_messages`: Query inbox using advanced Gmail search syntax (e.g., `is:unread from:boss after:2026/10/01`).
- `gmail_get_message`: Retrieve complete email body, headers, attachments metadata, and sender information.
- `gmail_list_threads`: View grouped conversation threads.
- `gmail_create_draft`: Compose email drafts with subject, recipients, and rich body formatting.
- `gmail_send_message`: Dispatch emails directly to recipients (guarded by HITL Safety Interceptor).
- `gmail_reply_message`: Context-aware inline reply to active conversation threads.
- `gmail_modify_labels`: Add or remove system labels (`UNREAD`, `STARRED`, `IMPORTANT`, custom project labels).
- `gmail_trash_message` / `gmail_delete_message`: Soft-delete or permanently purge emails (guarded by HITL).

#### B. Google Drive Suite
- `drive_search_files`: Full-text search across documents, spreadsheets, PDFs, and folders.
- `drive_get_file_metadata`: Inspect file ownership, MIME types, modification timestamps, and permissions.
- `drive_upload_file`: Upload local files to specific Drive folders.
- `drive_create_folder`: Scaffolds organized directory hierarchies in cloud storage.
- `drive_share_file`: Update Access Control Lists (ACLs) to grant viewer or editor permissions to colleagues.
- `drive_move_file`: Reorganize files between cloud folders.

#### C. Google Calendar Suite
- `calendar_list_events`: Fetch upcoming meetings, agendas, attendees, and meeting links.
- `calendar_search_events`: Query schedule by keywords or attendee email addresses.
- `calendar_create_event`: Book new appointments with title, start/end timestamps, location, and Google Meet video links.
- `calendar_update_event`: Reschedule, edit description, or update attendees.
- `calendar_delete_event`: Cancel scheduled appointments.

#### D. Google Docs & Google Sheets Suite
- `docs_create_document`: Generate new Google Docs from templates or markdown.
- `docs_get_document`: Extract structured document headings and paragraphs.
- `docs_insert_text`: Append synthesized notes, summaries, or meeting transcripts into active documents.
- `sheets_get_spreadsheet`: Read tabular worksheet grids.
- `sheets_append_row`: Insert new rows of metrics, test results, or inventory records into spreadsheets.

---

### 2.2 GitHub MCP (26 Tools)
Connected via `@modelcontextprotocol/server-github` using fine-grained Personal Access Tokens.

- `search_repositories`: Find repositories by keywords, stars, language, or organization.
- `get_repository`: Retrieve repository metadata, default branches, and clone URLs.
- `list_issues`: Fetch open/closed issues with labels, assignees, and milestone status.
- `get_issue`: Inspect detailed issue description, comments, and reaction threads.
- `create_issue`: Open new bug reports or feature requests with labels and assignees.
- `add_issue_comment`: Post updates, triage findings, or automated test summaries to active issues.
- `list_pull_requests`: Monitor open PRs, reviewers, base branches, and mergeability state.
- `get_pull_request`: Deep inspection of PR diffs, commits, and review status.
- `create_pull_request`: Submit code changes from feature branches into main.
- `create_pull_request_review`: Post automated code review comments, approvals, or request changes.
- `merge_pull_request`: Merge approved pull requests (guarded by HITL).
- `get_file_contents`: Read source code directly from any branch or commit SHA.
- `create_or_update_file`: Commit file edits or new source files directly to GitHub branches.
- `list_commits`: Audit commit history, author signatures, and commit messages.
- `search_code`: Search across entire GitHub repositories for specific symbols, variables, or functions.

---

### 2.3 YouTube Data MCP (6 Tools)
Connected via `mcp-youtube` using Google Cloud YouTube Data API v3.

- `youtube_search_videos`: Search YouTube for technical lectures, keynote presentations, tutorials, and podcasts.
- `youtube_get_video_details`: Retrieve title, channel, duration, view counts, and published dates.
- `youtube_get_transcript`: Extract full verbatim timed transcripts and subtitles from any public or unlisted video.
- `youtube_list_channel_videos`: Enumerate recently uploaded content from specified technical channels.
- `youtube_download_captions`: Retrieve closed-caption data in structured JSON or plaintext format.
- `youtube_summarize_content`: In conjunction with Gemini 2.5 Flash, generate executive chapter-by-chapter summaries of long-form videos.

---

### 2.4 Local Desktop Filesystem MCP (14 Tools)
Connected via `@modelcontextprotocol/server-filesystem`, dynamically scoped to the user's active Desktop.

- `read_file`: Read contents of text files, code files, markdown, or JSON directly on Desktop.
- `write_file`: Create new scripts, notes, or exported reports on Desktop (guarded by HITL).
- `edit_file`: Apply targeted, unified diff replacements to existing desktop code files without overwriting the entire file.
- `list_directory`: Enumerate subdirectories and files on the user's Desktop.
- `directory_tree`: Generate visual ASCII directory hierarchies of projects on Desktop.
- `search_files`: Locate files matching wildcard patterns or regex on Desktop.
- `get_file_info`: Inspect size, creation timestamps, and permissions of local desktop files.
- `move_file`: Rename or organize files into target folders on Desktop.

---

## 3. Cognitive Brain Capabilities

### 3.1 IntentRouter
- **Regex & Semantic Classifier:** Instant zero-shot routing into `plan`, `chat`, `execute`, or `suggestion`.
- **Latency Optimization:** Bypasses tool calling for conversational queries ("what is MATE?", "give me tips on organizing my inbox"), saving over 80% token overhead.

### 3.2 MATEPlanner
- **Task Decomposition:** Breaks down complex, multi-action goals into sequenced milestones.
- **Risk Scoring:** Assigns color-coded risk levels:
  - 🟢 **Low Risk:** Read-only operations (searching emails, listing PRs, inspecting directories).
  - 🟡 **Medium Risk:** Non-destructive creations (creating a draft email, creating a new local branch).
  - 🔴 **High Risk:** Modifying or public operations (sending emails, deleting files, merging pull requests).

### 3.3 SuggestionEngine
- **Proactive Follow-ups:** Automatically predicts the user's next logical step after every task.
- **1-Click Execution:** Triggers follow-up actions instantly when the user types `1`, `2`, or `3`, or clicks the visual badge in the Web UI.

### 3.4 SessionMemory
- **Multi-Turn Rolling Window:** Retains up to 15 prior turns of context, remembering previous query outputs, file paths, and repo names.
- **Context Summarization:** Compresses older interactions to maximize the effective Gemini 1M context window.

---

## 4. Web Dashboard & User Experience (UX) Features

- **Obsidian Dark Glassmorphism:** Custom aesthetic with backdrop blur (`blur(16px)`), electric indigo accents (`#6366F1`), and cyber cyan glows (`#06B6D4`).
- **Interactive Tool Explorer:** Searchable, filterable modal listing all 165+ connected tools, input parameters, and descriptions.
- **Real-Time Platform Indicators:** Dynamic pulsing status orbs reflecting live connection health for Google, GitHub, YouTube, and Desktop.
- **Human-in-the-Loop Confirmation Modal:** Prominent popup showing exact command parameters, recipient emails, and risk warnings before destructive execution.
- **Local Document Context Ingestion:** Drag-and-drop or upload local documents (`.txt`, `.md`, `.py`) to provide instant grounding context for the AI co-pilot.
