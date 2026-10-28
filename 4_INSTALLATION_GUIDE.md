# 🛠️ Deliverable 4: Installation Guide & Runbook

**Project:** MATE — Intelligent Multi-Platform AI Co-Pilot  
**Team:** Alpha And Beta  
**Lead:** Gowtham Datta Sai V | **Member:** Sai Teja  
**Date:** October 2026  
**Target Environment:** Windows 10/11, macOS, Linux  

---

## 1. System Requirements & Prerequisites

Before running MATE, ensure your host environment meets the following specifications:

| Requirement | Minimum Specification | Recommended |
| :--- | :--- | :--- |
| **Operating System** | Windows 10/11 64-bit, macOS 13+, or Ubuntu 22.04+ | Windows 11 (tested with native Desktop resolver) |
| **Python** | Python 3.11+ | Python 3.11 or 3.12 |
| **Package Manager** | `uv` (ultra-fast Python package installer) | `uv` v0.4.0+ |
| **Node.js runtime** | Node.js v18.0.0+ with `npx` | Node.js v20 LTS |
| **Network** | Outbound HTTPS access to Google APIs, GitHub, YouTube | High-speed internet |

---

## 2. Step-by-Step Installation Runbook

### Step 1: Install `uv` Package Manager (if not installed)
MATE uses `uv` for blazing-fast, deterministic virtual environment management.

- **Windows (PowerShell):**
  ```powershell
  powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
  ```
- **macOS / Linux:**
  ```bash
  curl -LsSf https://astral.sh/uv/install.sh | sh
  ```

### Step 2: Clone or Open the Repository
```powershell
cd C:\MATE
```

### Step 3: Install Dependencies
Run `uv sync` to automatically initialize the `.venv` virtual environment and install all dependencies pinned in `uv.lock`:
```powershell
uv sync
```

---

## 3. API Key & Credentials Configuration

### 3.1 Configure `.env`
Create or edit the `.env` file in the root `c:\MATE\` folder:

```ini
# ==========================================
# 1. Google Gemini LLM Credentials
# ==========================================
GEMINI_API_KEY=AIzaSyYourGeminiApiKeyHere
GEMINI_MODEL=gemini/gemini-2.5-flash

# ==========================================
# 2. Google Workspace Identity
# ==========================================
USER_GOOGLE_EMAIL=your.email@gmail.com

# ==========================================
# 3. GitHub Personal Access Token (PAT)
# ==========================================
# Generate at: https://github.com/settings/tokens (classic or fine-grained with repo/read/write scopes)
GITHUB_TOKEN=ghp_YourGitHubPersonalAccessTokenHere

# ==========================================
# 4. YouTube Data API Key
# ==========================================
# Enable "YouTube Data API v3" in Google Cloud Console
YOUTUBE_API_KEY=AIzaSyYourYouTubeApiKeyHere
```

### 3.2 Google Workspace OAuth Credentials (`credentials.json`)
The Google Workspace MCP server authenticates via official Google OAuth 2.0.

1. Go to **[Google Cloud Console](https://console.cloud.google.com/)**.
2. Create a project (e.g. `MATE-Copilot`).
3. Under **APIs & Services > Library**, enable:
   - Gmail API
   - Google Drive API
   - Google Calendar API
   - Google Docs API
   - Google Sheets API
4. Under **OAuth consent screen**, select **External**, configure your user email as a test user, and publish.
5. Under **Credentials**, click **Create Credentials > OAuth client ID** (Application type: **Desktop App**).
6. Download the JSON file, rename it to `credentials.json`, and place it in two locations:
   - `c:\MATE\credentials.json`
   - `%USERPROFILE%\.google_workspace_mcp\credentials\credentials.json`
   
   *(On Windows PowerShell, copy with:*
   ```powershell
   New-Item -ItemType Directory -Force -Path "$HOME\.google_workspace_mcp\credentials"
   Copy-Item "c:\MATE\credentials.json" "$HOME\.google_workspace_mcp\credentials\credentials.json"
   ```
   *)*

---

## 4. Launching the Application

### Option A: 1-Click Launch (Recommended for Windows Judges)
Double-click the [`start_ui.bat`](file:///c:/MATE/start_ui.bat) file in the root directory.

This script automatically:
1. Validates the Python and `uv` environment.
2. Starts the FastAPI server and launches all 4 MCP adapters.
3. Automatically opens `http://localhost:8000` in your default web browser.

### Option B: Command Line (FastAPI Web Server)
```powershell
uv run python server.py
```
Open **[http://localhost:8000](http://localhost:8000)** in Chrome, Edge, or Firefox.

### Option C: Terminal CLI Co-Pilot
If you prefer a rich terminal interface without running a web browser:
```powershell
uv run python agent.py
```

### Option D: Generate Official Presentation Pitch Deck
```powershell
uv run python generate_ppt.py
```
This generates `MATE_Presentation.pptx` in the project root.

---

## 5. Verification & Health Check

After launching `server.py`, verify your installation:

1. **Web Dashboard Verification:**
   - Navigate to `http://localhost:8000`.
   - Verify the top navigation status indicators:
     - 📧 **Google Workspace:** `Active (119 tools)`
     - 🐙 **GitHub:** `Active (26 tools)`
     - ▶️ **YouTube:** `Active (6 tools)`
     - 💻 **Desktop:** `Active (14 tools)`
2. **API Endpoint Verification:**
   - In your browser or curl, check:
     ```powershell
     curl http://localhost:8000/api/status
     ```
   - Expect JSON output:
     ```json
     {
       "status": "online",
       "google_connected": true,
       "github_connected": true,
       "youtube_connected": true,
       "desktop_connected": true,
       "total_tools": 165
     }
     ```

---

## 6. Troubleshooting FAQ

### Q1: `Address already in use` (Port 8000 busy)
- **Fix:** Terminate any running Python process using port 8000:
  ```powershell
  Get-Process -Id (Get-NetTCPConnection -LocalPort 8000).OwningProcess | Stop-Process -Force
  ```
  Or change the port in `server.py` line 468 (`uvicorn.run(app, host="0.0.0.0", port=8080)`).

### Q2: Google OAuth browser authorization prompt appears
- **Fix:** On the first execution, Google Workspace MCP will print a one-time OAuth URL in the terminal or automatically open your browser. Sign in with the account specified in `USER_GOOGLE_EMAIL` and click **Allow**. The authorization token will be saved permanently in `~/.google_workspace_mcp/credentials/token.json`.

### Q3: `npx: command not found`
- **Fix:** Ensure Node.js is installed and added to your system `PATH`. Open a new PowerShell terminal and verify:
  ```powershell
  node -v
  npx -v
  ```

### Q4: Windows console Unicode character errors
- **Fix:** MATE includes native UTF-8 stream reconfiguration in both `agent.py` and `server.py`:
  ```python
  if sys.platform == "win32":
      sys.stdout.reconfigure(encoding="utf-8")
  ```
  Ensure PowerShell is configured with code page 65001 (`chcp 65001`).
