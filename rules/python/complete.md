# Global Antigravity Rules

These rules apply across **all workspaces**.
They define how you, the Agent, should behave and write code by default.

---

## 1. General Behavior

- Prioritize **clear, maintainable, production-grade code** over clever one-liners.
- Prefer **small, incremental changes** with clear explanations instead of large, opaque rewrites.
- Before performing **destructive actions** (deleting files, rewriting large sections), explicitly:
  - Explain what you intend to do.
  - Ask for confirmation, unless the user has clearly approved that specific change.
- When you’re unsure about requirements, **state your assumptions explicitly** and proceed conservatively.

---

## 2. Modern Python & Typing

- **Python Version & Typing**
  - Assume **Python 3.10+**.
  - Always use **type hints** for all function arguments and return values.
  - Use `typing` and/or built-in generics (`list[int]`, `dict[str, Any]`, etc.). Avoid untyped code for new/modified functions.

- **Data Validation**
  - For complex data structures, API payloads, or configuration, prefer **Pydantic `BaseModel`** over plain dictionaries.
  - Define models close to where they’re used (e.g., `schemas.py` or `models.py`) and reuse them across routes/services.
  - Use Pydantic for:
    - Request/response bodies in FastAPI.
    - Internal domain objects that benefit from validation.

- **Explicit Exports**
  - Use `__all__` in modules to make the public API explicit.
  - Only include functions, classes, and constants meant to be imported from other modules.
  - When refactoring, update `__all__` accordingly.

---

## 3. Code Style & Quality

- **Docstrings**
  - Every non-trivial function, method, and class MUST have a docstring.
  - Use **Google-style docstrings** with `Args:`, `Returns:`, and `Raises:` sections.
  - Example:

    ```python
    def add_user(user: User) -> None:
        """Add a new user to the repository.

        Args:
            user: User model instance containing validated user data.

        Raises:
            ValueError: If a user with the same ID already exists.
        """
    ```

- **Error Handling**
  - NEVER use bare `except:` blocks.
  - Always catch specific exceptions (e.g., `except ValueError:`, `except HTTPException:`).
  - Use the standard `logging` library instead of `print()` for errors, warnings, and debug info.
  - When adding logging, include enough context (IDs, parameters, state) to debug issues later.

- **Formatting & Structure**
  - Assume code will be formatted with **Black**.
  - Don’t waste time manually aligning whitespace or obsessing over minor formatting; focus on correctness and clarity.
  - Organize modules logically:
    - `main.py` or `app.py` as entrypoints.
    - `routers/`, `services/`, `schemas/`, `models/`, `core/`, etc. for structure in FastAPI projects.

- **Tests**
  - When adding or changing non-trivial behavior, add or update **tests**.
  - Prefer `pytest` style tests with clear, descriptive test function names.

---

## 4. Environment & Safety

- **Virtual Environments**
  - Assume code runs inside a **virtual environment (venv)**.
  - Prefer installing dependencies via `pip` into the active environment.
  - When suggesting commands, default to:
    - `pip install <package>` (inside venv)
    - Or `pip install -r requirements.txt` for project setup.

- **Script Entry Points**
  - Always wrap script execution logic in:

    ```python
    if __name__ == "__main__":
        main()
    ```

  - Never let script logic run on import. Modules should be import-safe.

- **Secrets & Credentials**
  - Never hardcode secrets (API keys, tokens, passwords).
  - If you need to introduce a secret, assume it will come from:
    - Environment variables (e.g. `os.environ["MY_SECRET"]`)
    - A secure configuration system managed by the user.

---

## 5. Deployment & Infrastructure

Target environment assumptions:

- Primary platform: **Render Web Service** (Python 3.10+).
- Typical app style: **FastAPI** + **Uvicorn**.

### 5.1 Server Binding & Port

- The application must listen on **`0.0.0.0`** and the port defined by the environment variable **`PORT`**.
- Assume default port **10000** when defining examples, but respect `$PORT` in actual code.

Example:

```bash
uvicorn main:app --host 0.0.0.0 --port $PORT
```

- Never bind to `127.0.0.1` in production configuration.

### 5.2 Start Command

- Default start command for web services:

```bash
uvicorn main:app --host 0.0.0.0 --port $PORT

```

- If the app file or app variable differs (`app.py`, `create_app()`, etc.), clearly state the correct start command and adapt accordingly.

### 5.3 Build Command & Dependencies

- Build command:

```bash
pip install -r requirements.txt

```

- Ensure that at minimum the following are included when appropriate:
    - `fastapi`
    - `uvicorn`
    - `pydantic`
    - Any other dependencies that are actually used (databases, auth libs, etc.).
- When introducing new dependencies:
    - Add them to `requirements.txt`.
    - Prefer well-maintained, mainstream libraries.

---

## 6. FastAPI & CORS (Critical)

This API will often be consumed by a **separate frontend** (e.g., hosted on cPanel or another domain). Correct CORS configuration is mandatory.

- Always add **`CORSMiddleware`** to the FastAPI app.
- Allow origins:
    - Use the specific production domain(s) when known (e.g., your cPanel domain).
    - For development examples, you may use `["*"]` but clearly label this as **dev-only**.

Example (to be adapted with real domains):

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

origins = [
    "https://your-frontend-domain.com",  # production frontend
    # "http://localhost:3000",  # dev example (optional)
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

```

- When setting up new FastAPI apps, treat CORS as **non-optional**.
- If the user hasn’t specified the exact domain yet, ask for it or:
    - Use a clearly marked placeholder (`https://your-frontend-domain.com`).
    - Explain the security implications of using `["*"]` beyond local development.

---

## 7. Communication in Antigravity

- When you produce plans, task lists, or artifacts:
    - Be explicit about **what you’re changing**, **why**, and **how to verify**.
    - Prefer:
        - Step-by-step plans
        - Brief rationales
        - Notes on potential edge cases
- When the user’s instructions conflict with these global rules:
    - Respect **safety and correctness first**.
    - Politely highlight the conflict and propose a safer alternative.
