You are an expert Next.js + DevOps engineer working inside the current project deployed on Render Free Tier.

Your job is to **set up a keep-alive mechanism** using:
- A lightweight `api/health` endpoint
- A GitHub Actions cron job that pings this endpoint every 14 minutes
- A README update explaining the setup

Follow this strict workflow:

STEPS (DO IN ORDER)
1) Create Route
2) Implement Logic
3) Create Action
4) Configure Cron
5) Add Curl Step
6) Docs

---

## 🔑 INPUTS

- Public Render base URL: {{render_base_url}}
  e.g. `https://your-app-name.onrender.com`
- Next.js app path (if not root, optional): {{app_path}}
  e.g. `/app` or `/frontend` — if empty, assume root.
- Any existing docs conventions or sections in README: {{docs_context}}

If some fields are missing, infer them from the repo and state your assumptions briefly.

---

## ⚙️ GLOBAL RULES

- Assume **Next.js App Router** structure (`app/` directory).
- Keep the health endpoint **ultra-lightweight**: no DB, no external calls.
- Do not add any heavy logic, loops, or allocations to the health route.
- Use **TypeScript** for `app/api/health/route.ts`.
- Use **GitHub Actions** for `.github/workflows/keep-alive.yml`.
- Use idiomatic, clear Markdown when editing `README.md`.

At the end, summarize all created/modified files.

---

## 1️⃣ STEP 1 — CREATE ROUTE (app/api/health/route.ts)

Goal: Ensure there is a dedicated health endpoint for keep-alive pings.

1. Confirm the project is using the **App Router** (presence of `app/` directory, routes as folders).
2. Create the directory structure if needed:

   - `app/api/health/route.ts`

3. If the file already exists:
   - Do **not** break existing behavior.
   - Adjust implementation only if it’s safe and still compatible with a lightweight health check.

**Output for this step:**

- Confirm the file path: `app/api/health/route.ts` created or updated.
- Short note on whether it was newly created or modified.

Then continue to STEP 2 automatically.

---

## 2️⃣ STEP 2 — IMPLEMENT LOGIC (Lightweight Health Check)

Goal: Implement a minimal, zero-heavy health endpoint.

1. Implement a `GET` handler using `NextResponse` that returns a `200 OK` JSON response.
2. Response payload should be lightweight and stateless, e.g.:

   ```ts
   import { NextResponse } from 'next/server';

   export async function GET() {
     return NextResponse.json(
       { status: 'alive', timestamp: new Date().toISOString() },
       { status: 200 }
     );
   }
   ```
3. Ensure:
    - No DB calls
    - No cache busting
    - No external API calls
    - No heavy computations

**Output for this step:**

- Show the full contents of `app/api/health/route.ts`.
- One sentence explaining why this is safe and lightweight for keep-alive pings.

Then continue to STEP 3 automatically.

---

## 3️⃣ STEP 3 — CREATE ACTION (.github/workflows/keep-alive.yml)

Goal: Add a GitHub Actions workflow file to ping the health endpoint.

1. Ensure the `.github/workflows/` directory exists. If not, create it.
2. Create a new file:
    - `.github/workflows/keep-alive.yml`
3. If a keep-alive workflow already exists:
    - Update it carefully instead of creating duplicates.
    - Preserve existing behavior if it’s already correct and only extend/adjust as needed.

**Output for this step:**

- Confirm creation or update of `.github/workflows/keep-alive.yml`.
- Short description of the job you’re about to define.

Then continue to STEP 4 automatically.

---

## 4️⃣ STEP 4 — CONFIGURE CRON (Every 14 Minutes)

Goal: Configure GitHub Actions to run on a schedule of `*/14 * * * *`.

1. Inside `.github/workflows/keep-alive.yml`, configure the `on.schedule`:
    
    ```yaml
    name: Render Keep-Alive
    
    on:
      schedule:
        # Runs every 14 minutes (Render sleeps after ~15 mins)
        - cron: '*/14 * * * *'
    
    ```
    
2. Ensure no conflicting triggers (like push) are added unless explicitly desired.
3. Keep the workflow name clear (e.g. `Render Keep-Alive`).

**Output for this step:**

- Show the `on:` section of `.github/workflows/keep-alive.yml`.
- Confirm that the cron expression is exactly `/14 * * * *`.

Then continue to STEP 5 automatically.

---

## 5️⃣ STEP 5 — ADD CURL STEP (Pinger Job)

Goal: Configure a job that uses `curl` to ping the health endpoint.

1. In `.github/workflows/keep-alive.yml`, define a job like:
    
    ```yaml
    jobs:
      ping:
        runs-on: ubuntu-latest
        steps:
          - name: Ping Render URL
            run: curl -I {{render_base_url}}/api/health
    
    ```
    
    - If `{{render_base_url}}` already includes a path, append `/api/health` correctly.
    - Use `I` (HEAD) or lightweight GET; keep it simple and low-bandwidth.
2. If environment variables or GitHub Secrets are preferred (e.g. `RENDER_BASE_URL`), adapt accordingly but keep the logic clear.

**Output for this step:**

- Show the full contents of `.github/workflows/keep-alive.yml`.
- Confirm the exact URL being pinged (resolved from `{{render_base_url}}`).

Then continue to STEP 6 automatically.

---

## 6️⃣ STEP 6 — DOCS (Update README.md)

Goal: Document the keep-alive cron job and health endpoint.

1. Open `README.md`.
2. Add or update a section such as:
    
    ```markdown
    ## Render Keep-Alive
    
    This project uses a scheduled GitHub Actions workflow to prevent Render Free Tier cold starts.
    
    - Health endpoint: `GET /api/health`
    - Workflow: `.github/workflows/keep-alive.yml`
    - Schedule: runs every 14 minutes (`*/14 * * * *`) to ping the health endpoint at:
    
      `{{render_base_url}}/api/health`
    
    ```
    
3. Place the section in a logical spot:
    - Near deployment docs, or
    - Under a “DevOps”, “Deployment”, or “Infrastructure” heading.
4. Keep language concise and clear.

**Output for this step:**

- Show the added or modified README section.
- Confirm that it mentions:
    - The cron schedule
    - The endpoint path (`/api/health`)
    - The purpose (preventing Render Free Tier cold starts)

---

## 📦 WHAT THE AGENT WILL BUILD

When this workflow runs successfully, you must ensure these final artifacts exist:

1. **The Lightweight Endpoint** — `app/api/health/route.ts`
    
    ```tsx
    import { NextResponse } from 'next/server';
    
    export async function GET() {
      return NextResponse.json(
        { status: 'alive', timestamp: new Date().toISOString() },
        { status: 200 }
      );
    }
    
    ```
    
2. **The Pinger Workflow** — `.github/workflows/keep-alive.yml`
    
    ```yaml
    name: Render Keep-Alive
    
    on:
      schedule:
        # Runs every 14 minutes (Render sleeps after 15 mins)
        - cron: '*/14 * * * *'
    
    jobs:
      ping:
        runs-on: ubuntu-latest
        steps:
          - name: Ping Render URL
            run: curl -I https://YOUR-APP-NAME.onrender.com/api/health
    
    ```
    
3. **Docs Update** — `README.md`
    - A section explaining:
        - That the keep-alive cron job exists.
        - It prevents Render Free Tier cold starts.
        - Where the workflow and endpoint live.

---

## ✅ FINAL OUTPUT FORMAT

At the very end, respond with:

1. **Summary**
    - 3–5 bullets describing what was set up (endpoint, workflow, docs).
2. **Files Created/Modified**
    - `app/api/health/route.ts`
    - `.github/workflows/keep-alive.yml`
    - `README.md`
3. **Health Endpoint**
    - HTTP method, path, and example JSON response.
4. **Cron & Pinger**
    - Cron expression, GitHub Actions workflow name, and ping URL.
5. **How to Verify**
    - How to hit `/api/health` locally.
    - How to confirm the GitHub Action ran successfully (e.g. Actions tab).