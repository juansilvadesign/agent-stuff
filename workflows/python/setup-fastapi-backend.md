## 1. Scaffold the project structure

Goal: Ensure the basic files exist without destroying existing work.

// turbo
- Confirm the current directory is the intended project root.
- Check for the following files:
  - `main.py`
  - `requirements.txt`
  - `start.sh`
- For any that **do not** exist, create them.
- For any that **already exist**, do **not overwrite**:
  - If their content differs significantly from the template below, mention this to the user in the final summary and suggest a manual merge.

---

## 2. Create `main.py` with a FastAPI app

Goal: Provide a secure, Render-compatible FastAPI entrypoint with CORS and health check.

// turbo
If `main.py` does not exist, create it with **exactly** this structure (adapting only the domains in `origins` if the user provides them):

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
import os

app = FastAPI()

# ✅ Rule Applied: CORS is ready for your cPanel frontend
origins = [
    "https://your-cpanel-domain.com",
    "http://localhost:3000",  # For local testing
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,      # Or ["*"] if the user prefers wide-open CORS
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# ✅ Rule Applied: Health check for UptimeRobot
@app.get("/health")
async def health_check():
    return {"status": "alive"}

# ✅ Rule Applied: Standard Root
@app.get("/")
async def read_root():
    return {"message": "Hello from Render!"}

if __name__ == "__main__":
    import uvicorn
    # ✅ Rule Applied: Correct host/port binding for Render
    port = int(os.environ.get("PORT", 10000))
    uvicorn.run(app, host="0.0.0.0", port=port)
    
```
If `main.py` already exists:

- Check if it defines:
    - `app = FastAPI()`
    - A `GET /health` endpoint returning `{"status": "alive"}`
    - Proper CORS middleware with `CORSMiddleware`
    - `uvicorn.run(app, host="0.0.0.0", port=...)` using `PORT` from env or a sensible default
- If any of these are missing, **do not overwrite the file**. Instead:
    - Note the missing parts in the final summary as **recommended changes**.

---

## 3. Configure CORS for cPanel + local frontend

Goal: Ensure the cPanel frontend and local dev frontend can call the API without browser CORS errors.

// turbo

- Locate the `origins` list inside `main.py`.
- If the user has specified a cPanel domain, ensure it’s present, e.g.:
    - `"https://your-cpanel-domain.com"`
- Always include `http://localhost:3000` for common local frontend dev.
- Optionally, if the user prefers a permissive setup, you may set:
    - `allow_origins=["*"]`
- In the final summary:
    - Explicitly list which origins are currently allowed.

---

## 4. Ensure health check endpoint exists

Goal: Provide a stable endpoint for uptime checks (UptimeRobot, Render health checks, etc.).

// turbo

- Confirm there is a `GET /health` endpoint returning a JSON object with at least:
    - `{"status": "alive"}`
- If missing, add:

```python
@app.get("/health")
async def health_check():
    return {"status": "alive"}

```

- Mention in the final summary that `/health` is ready.

---

## 5. Create `requirements.txt` for dependencies

Goal: Make sure the core FastAPI dependencies are pinned at least at the package name level.

// turbo

If `requirements.txt` does **not** exist, create it with **at least**:

```
fastapi
uvicorn
pydantic

```

If it **does** exist:

- Ensure these packages are present (even if with versions).
- If any are missing, **append** them rather than removing or changing existing constraints.

---

## 6. Create `start.sh` for local development

Goal: Provide a simple, one-command way to start the dev server with reload enabled.

// turbo

If `start.sh` does **not** exist, create it with:

```bash
#!/usr/bin/env bash
uvicorn main:app --reload

```

Then:

- Make sure the script is executable (if the environment supports it), e.g. by suggesting:
    - `chmod +x start.sh`

If `start.sh` already exists, do not overwrite it, but:

- Check if it runs `uvicorn main:app --reload`.
- If not, note in the summary that the user may want to update it.

---

## 7. (Optional) Install dependencies in the current environment

If the user is working inside a Python environment and requests it, you may run:

```bash
pip install -r requirements.txt

```

- If installation fails, capture and summarize the error in the final report.

---

## 8. Final Summary for the User

At the end, provide a concise report covering:

1. **Files created or verified**
    - `main.py`: created / already existed (and whether it matches the template).
    - `requirements.txt`: created / updated / already OK.
    - `start.sh`: created / already existed.
2. **CORS configuration**
    - List the current `origins`.
    - Confirm that cPanel domain + `http://localhost:3000` are allowed (or that `["*"]` is used).
3. **Health check**
    - Confirm `/health` exists and returns `{"status": "alive"}`.
4. **Render / deployment readiness**
    - Confirm:
        - `uvicorn.run` binds to `0.0.0.0`
        - Port reads from `os.environ["PORT"]` with a default like `10000`.
5. **Next steps**
    - How to run locally:
        - `./start.sh` (or `bash start.sh`)
    - Suggest deploying to Render or another platform using `main:app` as entrypoint.

End with a short verdict:

- `"✅ FastAPI service is ready for local dev and Render deployment."`
- Or `"⚠️ Some manual changes are recommended before deployment:"` followed by a short checklist.
