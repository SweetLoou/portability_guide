---

# Windows Dev Stack (Clean Install Guide)

Target: Fresh Windows 11 Computer
Stack: Python 3.14 (uv), Node v24, VS Code + Cursor

---

### Phase 1: The "One-Shot" Install
We use Winget (Microsoft's Package Manager) to install everything in the correct dependency order.

1.  Open PowerShell or Terminal as Administrator.
2.  Paste this entire block and hit Enter:

# 1. Install Runtimes & Core
winget install -e --id Git.Git
winget install -e --id Python.Python.3.14 --scope machine
winget install -e --id OpenJS.NodeJS.LTS --scope machine

# 2. Install Modern Tooling (The "uv" manager)
winget install -e --id astral-sh.uv

# 3. Install Editors
winget install -e --id Microsoft.VisualStudioCode
winget install -e --id Anysphere.Cursor

---

### Phase 2: The "Path Fix" & Tooling (Crucial)
*This step fixes the "uv not found" error you encountered.*

1.  Close your Administrator Terminal.
2.  Open a normal PowerShell window.
3.  Run this command to force Windows to recognize uv:
        uv tool update-shell
    
4.  Close the window again and re-open it (this applies the path change).
5.  Now, install your global linters efficiently:
        uv tool install ruff
    uv tool install pyright
    

---

### Phase 3: Editor Configuration (Cursor)
We need to install the specific extensions that match the tools above.

1.  Open Cursor.
2.  If prompted: Click "Install 'cursor'" (keep 'code' for VS Code).
3.  Go to Extensions (Ctrl+Shift+X) and install these 7 Essentials:

| Category | Extension Name | Why? |
| :--- | :--- | :--- |
| Python | Python (Microsoft) | Core language support. |
| Python | Ruff (Astral Software) | Replaces Pylance/Black/Flake8 (Speed). |
| Node | Prettier (Prettier) | Standard formatter. |
| Node | ESLint (Microsoft) | Logic checker. |
| Node | Pretty TypeScript Errors | Makes errors readable. |
| Utils | GitLens | Shows who wrote the code and when. |
| Utils | DotENV | Syntax highlighting for .env files. |

---

### Phase 4: The "Secret Sauce" Settings
This forces Cursor to use the fast tools (ruff) instead of the slow defaults.

1.  Press Ctrl + Shift + P $\rightarrow$ Type "User Settings (JSON)".
2.  Paste this configuration:

{
  // --- PYTHON SETUP (2025 Standard) ---
  "[python]": {
    "editor.defaultFormatter": "charliermarsh.ruff",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll": "explicit",
      "source.organizeImports": "explicit"
    }
  },

  // --- NODE/WEB SETUP ---
  "[javascript]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
  "[typescript]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
  "[json]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },

  // --- GENERAL EDITOR ---
  "editor.formatOnSave": true,
  "terminal.integrated.defaultProfile.windows": "PowerShell",
  "files.trimTrailingWhitespace": true,
  
  // --- FIX CURSOR/VSCODE UI CONFLICTS ---
  "workbench.activityBar.orientation": "vertical"
}

---

### Phase 5: How to Work (The 2025 Workflow)

Since you are set up for the future, your commands change slightly:

1. Starting a Python Project
Don't use python -m venv or pip install. Use uv.
mkdir my-project
cd my-project
uv init                  # Creates project
uv add pandas requests   # Installs packages INSTANTLY
cursor .                 # Opens editor

2. Starting a Node Project
Standard procedure.
mkdir my-web-app
cd my-web-app
npm init -y
npm install react
cursor .

3. Switching Editors
*   Type cursor . $\rightarrow$ Opens AI Editor (Daily Driver).
*   Type code . $\rightarrow$ Opens Standard VS Code (Backup/Legacy).
