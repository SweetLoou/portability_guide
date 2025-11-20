# 💾 Code Portability: Rules, Workflow & Configuration

This document defines the **System Instructions**, **Workflow**, and **Context** for the AI assistant in this workspace. These rules ensure code portability, robustness, and adherence to modern Python best practices.

***

## 🟢 Rules

**Use these as always‑on instructions for the AI in this workspace.**

### 1. General Behavior and Safety
*   **Preserve Behavior:** Preserve existing behavior unless something is clearly a bug.
*   **Explicit Changes:** Call out any behavior changes explicitly in your response.
*   **Dependencies:** Do not add new runtime dependencies unless clearly justified; list any new dependencies and why they’re needed.
*   **Copy-Paste Ready:** Keep examples and edits copy‑paste‑ready (no ellipses or pseudo‑code unless explicitly requested).

### 2. Portability Assumptions
*   **Target:** Python 3.8+ (primarily CPython) supporting Windows, macOS, and Linux.
*   **Internals:** Avoid CPython‑specific internals/C‑extensions unless the codebase already relies on them and the change is local.
*   **Execution:** Do not assume `python` is the executable. Be OS‑aware (`py` on Windows, `python3` on Unix), or prefer `sys.executable` and `python -m` invocations.

### 3. Paths and Filesystem
*   **Pathlib:** Prefer `pathlib.Path` over raw string paths for all new or refactored code.
*   **Derivation:** For scripts, derive paths via `BASE_DIR = Path(__file__).resolve().parent`.
*   **Resources:** For installed libraries, prefer `importlib.resources.files("package")` over `__file__`.
*   **Case Sensitivity:** Always respect filename case (assume case‑sensitive filesystems).
*   **User Dirs:** Use `Path.home()` and `tempfile` for user/temp directories; avoid hard-coding.

### 4. Text I/O, Encodings, and Newlines
*   **Encoding:** Always specify `encoding="utf-8"` for project-controlled text files (JSON, CSV, Markdown, config, logs).
*   **External Text:** For unknown sources, consider `encoding="utf-8", errors="replace"` and explain the choice.
*   **Newlines:** Use `newline=""` **only** for CSV/libraries requesting it. Do not use it for generic text I/O.

### 5. Configuration and Secrets
*   **Secrets:** Prefer environment variables. **Never** hard‑code API keys or tokens.
*   **Dotenv:** Only introduce `.env` if no config system exists. Load via `load_dotenv()` only in entrypoints (not libraries).
*   **Validation:** Validate required env vars at startup; fail fast with clear error messages.

### 6. Logging and Error Handling
*   **No Print:** Use `logging` instead of `print()` for diagnostics.
*   **Apps:** Configure logging to a file (under `logs/`) and console.
*   **Libs:** Use module‑level loggers (`logger = logging.getLogger(__name__)`); do not configure global handlers.
*   **Entrypoints:** Wrap execution in `main() -> int` and guard with `if __name__ == "__main__": raise SystemExit(main())`.
*   **Exceptions:** Log unhandled exceptions via `logger.exception(...)` and exit non‑zero.

### 7. Imports and Missing Dependencies
*   **No Prompts:** Do not perform interactive prompts at import time.
*   **Missing Deps:** In CLIs, catch `ImportError`, print clear installation instructions to `stderr`, and exit non‑zero.
*   **Libs:** Declare dependencies in `pyproject.toml` rather than handling missing packages at runtime.

### 8. Subprocesses and OS‑Specifics
*   **Subprocess:** Prefer `subprocess.run([...], check=True)` over `os.system`.
*   **Commands:** Use `sys.executable` rather than hard-coded `python`.
*   **Cosmetics:** Encapsulate OS‑specific cosmetics (e.g., clear screen) in helper functions; treat them as optional.

### 9. Virtual Environments
*   **Naming:** Assume per‑project venvs named `.venv`.
*   **Creation:** Prefer `py -3.x -m venv .venv` (Windows) or `python3.x -m venv .venv` (macOS/Linux).
*   **Scope:** Do not encourage global `pip` installs.

### 10. Packaging and Dependencies
*   **Applications:** `requirements.txt` with pinned versions is acceptable.
*   **Libraries:** Prefer `pyproject.toml` with `requires-python`, dependencies, and classifiers.
*   **Separation:** Keep runtime deps separate from dev/test tools.

### 11. Testing and CI
*   **Cross-Platform:** Assume tests run on Windows, macOS, and Linux.
*   **Stability:** Avoid breaking existing tests; if necessary, update them and explain why.
*   **Coverage:** When introducing non‑trivial changes, suggest/add at least one test.

### 12. Code Style, Typing, and Documentation
*   **Type Hints:** **Always** add PEP 484 type hints to new function signatures and public APIs.
*   **Docstrings:** Update docstrings to reflect any changes in arguments, return types, or behavior. Do not leave stale documentation.
*   **Linters:** Respect existing `ruff`/`flake8`/`mypy` configurations.

### 13. Interaction
*   **Pauses:** Do not use `input("Press Enter...")` unless explicitly asked for an interactive script.
*   **Headless:** Code must be safe for cron, CI, and containerized environments.

---

## 🔄 Workflow

**Follow these steps for substantial work (refactoring, features, robustness).**

1.  **Understand & Classify**
    *   Is this an App (script/CLI) or Library?
    *   Identify existing patterns for paths, config, and logging.

2.  **Plan the Change**
    *   List intended changes (e.g., "Migrate to pathlib", "Add type hints").
    *   Identify potential behavior changes.
    *   Justify new dependencies.

3.  **Refactor for Portability & Robustness**
    *   **Paths:** Convert strings to `pathlib.Path`.
    *   **I/O:** Add `encoding="utf-8"`.
    *   **Secrets:** Move hard-coded keys to env vars.
    *   **Logging:** Replace `print` with `logging`.
    *   **Process:** Use `subprocess.run`.

4.  **Keep Behavior Stable**
    *   Do not change function signatures unless fixing a bug or refactoring explicitly.
    *   Document rationale for any behavior modification.

5.  **Integrate Tests, Types & Docs**
    *   **Typing:** Ensure new code is fully typed.
    *   **Docs:** Update docstrings immediately to match the new implementation.
    *   **Tests:** update or add tests to cover the change.

6.  **Summarize**
    *   Provide a concise summary of changes.
    *   List behavior changes (exit codes, errors).
    *   List new dependencies.

---

## 🧠 Context

**Overview of goals and constraints.**

*   **Purpose:** Apply consistent patterns for portability and robustness.
*   **Assumptions:** Python 3.8+, Cross-platform (Windows/macOS/Linux), Headless-safe.
*   **Project Types:** Detect App vs. Library and apply appropriate packaging standards.
*   **Env Management:** Per-project `.venv`.
*   **Secrets:** Env vars > `.env` > Hard-coded (Forbidden).
*   **Quality:** Structured logging, Type hints, Tests, and CI-readiness.

---

## 📚 Knowledge

**Compact knowledge base for decision making.**

1.  **Interpreters:** Target Python 3.8+ CPython. Support PyPy where possible.
2.  **Venv:** Windows: `py -3.x -m venv .venv`; Unix: `python3.x -m venv .venv`.
3.  **Filesystem:** Use `pathlib`. `BASE_DIR = Path(__file__).parent`. Use `importlib.resources` for library data.
4.  **Encoding:** Default to `encoding="utf-8"`. Use `newline=""` **only** for CSV.
5.  **Config:** `os.getenv` for secrets. `.env` allowed for apps if added to `.gitignore`.
6.  **Logging:** Apps: `basicConfig` (File+Console). Libs: `getLogger(__name__)` (NullHandler default).
7.  **Deps:** Libs use `pyproject.toml`. Apps use `requirements.txt`.
8.  **Subprocess:** `subprocess.run(..., check=True)`. Avoid `os.system`. Use `sys.executable`.
9.  **Testing:** Pytest preferred. Test across OSes.
10. **Packaging:** `python -m build`. Separate runtime vs dev deps.
11. **Environment:** UTC for times. Case-sensitive paths.
12. **Editor:** Assume VS Code + Official Python Extension. Select `.venv`.
