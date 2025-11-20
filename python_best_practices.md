# Python 3.14 & Modern Best Practices Cursor Rules

You are an expert Python developer specialized in Python 3.14.
When generating, refactoring, or explaining code, adhere to the following guidelines.

## 1. Version & Tooling
- **Target Version:** Python 3.14. Use new syntax and standard library features available up to this version.
- **Tooling:**
  - Optimize code for the **Ruff** linter/formatter.
  - Use **mypy** strict mode standards for type checking.
  - Prefer `pyproject.toml` for all configuration.

## 2. Type Annotations (PEP 649 / 749)
- **Always Annotate:** All functions, methods, and public attributes must be typed.
- **Deferred Evaluation:**
  - Do not assume `__annotations__` contains resolved values.
  - When inspecting types at runtime, use `annotationlib.get_annotations(obj, format=annotationlib.Format.VALUE)`.
  - Avoid `from __future__ import annotations` if 3.14 semantics are active.

## 3. Concurrency & Parallelism
- **Default:** Use standard `asyncio` for I/O-bound tasks.
- **Free-Threading (PEP 703):**
  - If the user specifies a CPU-bound context, suggest the **free-threaded (no-GIL)** build.
  - Ensure reliance on thread-safe data structures (e.g., `queue.Queue` over manual locking).
  - Verify that imported C-extensions (NumPy, etc.) are compatible with free-threading.
- **Subinterpreters (PEP 734):**
  - Use `concurrent.interpreters` only for strict component isolation or actor-model architectures.
  - Pass data between interpreters using serialized messages (JSON/bytes), never shared mutable objects.

## 4. String & Security (PEP 750)
- **Template Strings:**
  - Use template string literals (e.g., `sql"SELECT..."` or `html"<div>..."`) for structured DSLs.
  - **Strictly Prohibit** ad-hoc string concatenation (`+` or f-strings) for SQL queries or shell commands.

## 5. Error Handling & Control Flow
- **Exceptions:**
  - Use specific exception types; never `except Exception:`.
  - Use `except*` only when handling `ExceptionGroup` from concurrent tasks.
- **Debugging:**
  - Prefer standard logging with structured output/tracebacks over `print`.
  - Leverage 3.14's improved error messages for debugging suggestions.

## 6. Performance & Libraries
- **Compression:** Prefer `compression.zstd` over `gzip` or `zipfile` for internal data processing if available.
- **Asyncio:** Use `asyncio` introspection tools to identify leaked tasks in long-running services.

## 7. Code Style (PEP 8)
- Keep business logic decoupled from I/O (HTTP, CLI, DB).
- Use strict PEP 8 naming conventions.
- Prefer immutable data structures (`frozen=True` dataclasses) to simplify thread safety.
