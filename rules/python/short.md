# Global Antigravity Rules (Concise)

These rules apply to **all workspaces** and define how the Agent behaves by default.

---

## 1. General Behavior

- **Clarity & Maintainability:** Prefer clear, production-grade code over clever one-liners.
- **Incremental Changes:** Make small, well-explained edits instead of huge rewrites.
- **Assumptions:** When requirements are unclear, explicitly state assumptions.

---

## 2. Modern Python & Typing

- **Python 3.10+:** Always assume Python 3.10 or higher.
- **Type Hints:** Use type hints for all function arguments and return values.
- **Pydantic:** For complex data (API payloads, configs, domain models), use **Pydantic `BaseModel`** instead of raw dicts.
- **Explicit API:** Use `__all__` to define the public API of each module.

---

## 3. Code Style & Quality

- **Docstrings:** Use **Google-style docstrings** (`Args`, `Returns`, `Raises`) for non-trivial functions/classes.
- **Error Handling:**
  - Never use bare `except:`.
  - Catch specific exceptions and use the **`logging`** library instead of `print()`.
- **Formatting:** Assume code is formatted by **Black**; don’t micro-manage whitespace.
- **Tests:** For non-trivial changes, add or update **pytest** tests.

---

## 4. Environment & Safety

- **Virtualenv:** Assume code runs inside a **venv**. Use `pip install` in the active environment.
- **Entry Point:** Wrap script execution in:

  ```python
  if __name__ == "__main__":
      main()
  ```

- **Secrets:** Never hardcode secrets; assume they come from **environment variables** or a secure config system.

---

## 5. Deployment & Infrastructure (Render + FastAPI)

- **Platform:** Target **Render Web Service** with **Python 3.10+**.
- **Binding:** App must listen on `0.0.0.0` and **`PORT` env var** (default 10000).
- **Start Command (canonical):**
    
    ```bash
    uvicorn main:app --host 0.0.0.0 --port $PORT
    
    ```
    
    Never use `127.0.0.1` in production.
    
- **Build Command:**
    
    ```bash
    pip install -r requirements.txt
    
    ```
    
    Ensure at least: `fastapi`, `uvicorn`, `pydantic` (plus any used libs).
    

---

## 6. FastAPI & CORS (Critical)

- **Always configure CORS** when building APIs for a separate frontend.
- Use **`CORSMiddleware`** and set `allow_origins` to:
    - The real **production frontend domain** (cPanel or similar).
    - `["*"]` only for clearly marked **dev/local** use.
- Treat CORS as **mandatory**, not optional.

---

## 7. Conflicts & Safety

- If user instructions conflict with these rules:
    - Prioritize **safety, correctness, and security** first.
    - Briefly explain the conflict and propose a safer alternative.
- Avoid destructive actions (large rewrites, file deletions) without clear user intent.
