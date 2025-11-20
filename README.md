# Python Project Portability & Setup Guide

---

## 1. Environment Setup

The current machine may not have Python installed.

### Steps

1. Download Python from:  
   https://www.python.org/

2. During installation, you must enable:

[x] Add Python to PATH

3. Open VS Code and load the project folder using:  
**File → Open Folder…**

---

## 2. “Fix It” Prompt (Copy & Use)

Copy the entire block below and paste it into your AI assistant to repair portability, dependencies, and VS Code errors:

```

CONTEXT:
I have moved my Python project to a new drive/computer. The code currently uses hardcoded absolute paths (e.g., "C:/Users/..." or "E:/...") which are causing crashes. I also need to reinstall my libraries and fix my VS Code IntelliSense.

MY FOLDER STRUCTURE:
(Check my files or assume a standard structure: main.py at root, data folders nearby)

TASKS:

1. Make Paths Portable:

   * Refactor my code to use pathlib.
   * Use BASE_DIR = Path(**file**).parent.resolve().
   * Replace all absolute paths with BASE_DIR / "folder" / "file".

2. Identify Dependencies:

   * Look at my import statements.
   * Tell me the exact pip install command I need.

3. Fix IntelliSense:

   * Check if I have a .vscode/settings.json file.
   * If it has python.defaultInterpreterPath, tell me which lines to delete.
   * Ensure VS Code points to my new Python installation.

````

---

## 3. Implementing the Fix

### Portable Paths

Example conversion using `pathlib`:

```python
from pathlib import Path

BASE_DIR = Path(__file__).parent.resolve()
data_file = BASE_DIR / "data" / "example.csv"
````

### Reinstalling Libraries

Open the VS Code terminal:

* Windows/Linux: Ctrl + `
* macOS: Cmd + `

Run the pip install command identified by your AI assistant, for example:

```bash
pip install pandas numpy requests
```

### Cleaning VS Code Settings

If `.vscode/settings.json` contains an old Python path:

* Remove only the incorrect lines
  **or**
* Delete the entire `.vscode` folder to reset IntelliSense.

---

## 4. IntelliSense Reset (Manual)

If errors persist:

1. Press:

   * Windows/Linux: Ctrl + Shift + P
   * macOS: Cmd + Shift + P
2. Select: `Python: Select Interpreter`
3. Choose the recommended/global interpreter.
4. Restart VS Code.

---

## 5. Why These Steps Work

| Step                    | Purpose                                            |
| ----------------------- | -------------------------------------------------- |
| Environment Setup       | Ensures VS Code can find Python.                   |
| Path Refactoring        | Allows the project to run on any drive letter.     |
| Dependency Installation | Restores required libraries.                       |
| VS Code Reset           | Resolves IntelliSense and interpreter path issues. |

---
