## 1. Ensure you’re in the correct project directory

- Confirm the current working directory is the project root (where `pyproject.toml` and/or `requirements.txt` live).
- If unsure, list files and verify you see your main Python modules and config files.

---

## 2. Auto-format the code

Goal: Fix style issues automatically so reviews focus on logic, not formatting.

- Prefer `black` or `ruff format` depending on the project standard.
- If both are available, use the one defined in `pyproject.toml` or documented in the repo.

// turbo  
- Try (in this order, stopping at the first that works):
  1. `ruff format .`
  2. `black .`

- If the formatter fails:
  - Capture the error output.
  - Note whether it’s a **config issue** (missing `pyproject.toml`/config) or a **syntax issue** in the code.

---

## 3. Run type checks with mypy

Goal: Catch static type issues the agent may have missed.

// turbo  
- Run: `mypy .`

- If `mypy` is not installed:
  - Check if it’s declared in `requirements.txt` or `pyproject.toml`.
  - Suggest installing it (e.g. `pip install mypy`) or adding it to project dependencies.
- If `mypy` reports errors:
  - Group them by **file → error type**.
  - Highlight:
    - Incorrect return types
    - Incompatible assignments
    - Missing attributes
  - Mark errors as **Critical** if they clearly indicate runtime failures (e.g. obvious type mismatches).

---

## 4. Run lint checks

Goal: Find unused imports, unused variables, and common bug patterns.

Choose the linter based on the project setup:

// turbo  
- Prefer `ruff` if available:
  - `ruff check .`
- Otherwise, fall back to `flake8`:
  - `flake8`

Process the results:

- Group findings into:
  - **Style / Cosmetic** (line length, naming, etc.)
  - **Potential Bugs** (unused variables, unreachable code, bare `except`, etc.)
- Treat **Potential Bugs** as higher priority than purely stylistic issues.

---

## 5. Summarize critical issues for the user

Goal: Create a concise report that tells the user what to fix *before* committing.

Include in the summary:

1. **Formatting**
   - Which formatter was used (`black` or `ruff format`).
   - Whether it ran successfully or failed (and why).

2. **Type Checking (mypy)**
   - Number of files with type errors.
   - 3–5 most important errors, in this format:
     - `path/to/file.py:LINE - [error code] short description`
   - Call out:
     - Incompatible types
     - Missing attributes
     - Incorrect function signatures

3. **Linting (ruff/flake8)**
   - Number of lint issues detected.
   - Highlight any **critical patterns**, for example:
     - Unused variables in key functions
     - Unused imports indicating dead code
     - Suspicious `except:` blocks
   - Mention if the majority are just style issues.

4. **Critical Errors Needing Manual Attention**
   - Build a separate bullet list of *blocking* items:
     - Errors that likely cause runtime failures
     - Type errors in core modules
     - Lint warnings that indicate logic bugs
   - For each critical item, add a **brief suggestion** on how to fix it.

5. **Ready-to-Commit Verdict**
   - End with a clear status:
     - `"✅ Looks good for commit"` — if there are no critical issues.
     - `"⚠️ Some issues should be fixed before commit"` — if critical issues remain.
   - If not ready, list the top 3 fixes to do next.

---

## 6. Optional: Suggest follow-up improvements

If the project doesn’t have them yet, recommend:

- Adding these tools to `pyproject.toml` or `requirements.txt`.
- Creating a pre-commit hook that runs:
  - Formatter
  - Linter
  - Type checker

So future commits automatically respect the same code quality pipeline.
