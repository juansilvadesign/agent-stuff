You are an expert Next.js + DevOps engineer working inside the current project deployed on Render Free Tier.

Your job is to **set up a keep-alive mechanism using UptimeRobot** by:

- Creating a cache-busting `api/health` endpoint in Next.js (App Router)
- Verifying it locally
- Documenting how to connect it to UptimeRobot

Follow this strict workflow:

STEPS (DO IN ORDER)
1) Create Route
2) Implement Logic
3) Test
4) Documentation

---

## 🔑 INPUTS

- Public Render base URL: {{render_base_url}}
  e.g. `https://your-project-name.onrender.com`
- Next.js app path (if not root, optional): {{app_path}}
  e.g. `/app` or `/frontend` — if empty, assume root.
- Docs conventions for `docs/` (if any): {{docs_context}}

If some fields are missing, infer them from the repo and briefly state your assumptions.

---

## ⚙️ GLOBAL RULES

- Assume **Next.js App Router** with an `app/` directory.
- Create `app/api/health/route.ts` (or adjust if it already exists).
- The endpoint must:
  - Return `200 OK`
  - Return JSON with `{ status: 'alive', timestamp: new Date().toISOString() }`
  - Send **cache-busting headers** (`Cache-Control: no-store` etc.) to ensure the request always hits the server, not a CDN cache.
- Do **not** add heavy logic (no DB, no external calls, no heavy computation).
- Documentation must go into: `docs/UPTIME_MONITOR.md`.

At the end, summarize all created/modified files.

---

## 1️⃣ STEP 1 — CREATE ROUTE (app/api/health/route.ts)

Goal: Ensure there is a dedicated health endpoint using the Next.js App Router.

1. Confirm the project is using the **App Router** (presence of `app/` directory).
2. Create the directory structure if needed:

   - `app/api/health/route.ts`

3. If `app/api/health/route.ts` already exists:
   - Preserve behavior where sensible, but ensure it’s compatible with a lightweight keep-alive health check.

**Output for this step:**

- Confirm that `app/api/health/route.ts` exists (created or updated).
- One sentence indicating whether it was newly created or modified.

Then continue to STEP 2 automatically.

---

## 2️⃣ STEP 2 — IMPLEMENT LOGIC (Cache-Busting Health Check)

Goal: Implement a minimal, cache-busting health endpoint.

1. Implement a `GET` handler using `NextResponse` that returns JSON with status and timestamp.
2. Add response headers that **disable caching** so that Render receives the request every time:

   ```ts
   // app/api/health/route.ts
   import { NextResponse } from 'next/server';

   export async function GET() {
     return NextResponse.json(
       { status: 'alive', timestamp: new Date().toISOString() },
       {
         status: 200,
         headers: {
           // Forces Render to process the request every time
           'Cache-Control': 'no-store, no-cache, must-revalidate, proxy-revalidate',
           Pragma: 'no-cache',
           Expires: '0',
         },
       }
     );
   }
   ```
3. Ensure:
    - No database calls
    - No external APIs
    - No heavy operations

**Output for this step:**

- Show the full contents of `app/api/health/route.ts`.
- One short explanation of how the headers prevent CDN caching and help wake up the server.

Then continue to STEP 3 automatically.

---

## 3️⃣ STEP 3 — TEST (Local Verification)

Goal: Confirm the health route works locally.

1. Run the dev server:
    
    ```bash
    npm run dev
    
    ```
    
    (If the project uses `yarn` or `pnpm`, adapt accordingly.)
    
2. Request the health endpoint locally:
    - URL: `http://localhost:3000/api/health`
        
        (Adjust the port if the project uses a different one.)
        
3. Verify that:
    - Response status is `200`.
    - Response JSON looks like:
        
        ```json
        {
          "status": "alive",
          "timestamp": "2025-01-01T00:00:00.000Z"
        }
        
        ```
        
    - Response headers include `Cache-Control: no-store, no-cache, must-revalidate, proxy-revalidate` and the other no-cache headers.
4. If the endpoint fails:
    - Capture and summarize the error.
    - Fix any route or import issues and re-test.

**Output for this step:**

- Command used to start the dev server.
- Example response (status + body) from hitting `/api/health`.
- Confirmation that headers are present and correct.

Then continue to STEP 4 automatically.

---

## 4️⃣ STEP 4 — DOCUMENTATION (docs/UPTIME_MONITOR.md)

Goal: Provide a step-by-step guide for configuring UptimeRobot to keep Render awake.

1. Ensure a `docs/` directory exists. If not, create it.
2. Create a new file:
    - `docs/UPTIME_MONITOR.md`
3. Write a clear, step-by-step guide that includes:
    - Explanation of **why** this exists (prevent Render Free Tier cold starts by hitting the health endpoint regularly).
    - Instructions to go to **UptimeRobot**:
        - Open: https://uptimerobot.com/
        - Create a free account (if needed).
    - Steps to create a new monitor:
        
        ```markdown
        1. Go to **UptimeRobot** and log in.
        2. Click **Add New Monitor**.
        3. Set **Monitor Type** to `HTTP(s)`.
        4. Set **Friendly Name** to something like `Render Keep-Alive`.
        5. Set **URL** to your deployed Render URL + `/api/health`, for example:
        
           `https://[YOUR-PROJECT-NAME].onrender.com/api/health`
        
        6. Set **Monitoring Interval** to `10 minutes` (safe margin below the 15-minute sleep limit on Render Free Tier).
        7. Save the monitor.
        
        ```
        
    - Mention that this endpoint returns a JSON `{ status: 'alive', timestamp: ... }` and uses cache-busting headers so every check hits the server.
4. Optionally, add a short “How it works” section:
    
    ```markdown
    ## How it works
    
    - The `/api/health` endpoint is a lightweight route that always responds with `200 OK`.
    - UptimeRobot calls this endpoint every 10 minutes.
    - Each request wakes the Render service and prevents it from going to sleep due to inactivity.
    
    ```
    
5. Make sure the doc is self-contained and understandable without reading the code.

**Output for this step:**

- The full contents (or a concise but complete excerpt) of `docs/UPTIME_MONITOR.md`.
- Confirmation that:
    - The URL format is documented (`https://[YOUR-PROJECT-NAME].onrender.com/api/health`).
    - The interval is set to **10 minutes**.
    - UptimeRobot monitor type `HTTP(s)` is clearly described.

---

## 📦 WHAT THE AGENT WILL GENERATE

When this workflow runs successfully, you must ensure these artifacts exist:

1. **Cache-busting Health Endpoint** — `app/api/health/route.ts`
    
    ```tsx
    import { NextResponse } from 'next/server';
    
    export async function GET() {
      return NextResponse.json(
        { status: 'alive', timestamp: new Date().toISOString() },
        {
          status: 200,
          headers: {
            // Forces Render to process the request every time
            'Cache-Control': 'no-store, no-cache, must-revalidate, proxy-revalidate',
            Pragma: 'no-cache',
            Expires: '0',
          },
        }
      );
    }
    
    ```
    
2. **Uptime Monitor Documentation** — `docs/UPTIME_MONITOR.md`
    - Explains the purpose of the health endpoint.
    - Shows how to configure UptimeRobot:
        - Monitor Type: `HTTP(s)`
        - URL: `https://[YOUR-PROJECT-NAME].onrender.com/api/health`
        - Interval: **10 minutes**.
3. **Manual Follow-Up After Deployment**
    
    Since the Agent cannot go to UptimeRobot for you, clearly instruct the user to:
    
    1. Go to **UptimeRobot** (free account).
    2. Click **Add New Monitor**.
    3. Select **Monitor Type:** `HTTP(s)`.
    4. Set **Friendly Name:** `Render Keep-Alive`.
    5. Set **URL:** Render deployment URL + `/api/health`.
    6. Set **Monitoring Interval:** `10 minutes`.

---

## ✅ FINAL OUTPUT FORMAT

At the very end, respond with:

1. **Summary**
    - 3–5 bullets describing what was set up (endpoint, cache-busting headers, documentation).
2. **Files Created/Modified**
    - `app/api/health/route.ts`
    - `docs/UPTIME_MONITOR.md`
3. **Health Endpoint Details**
    - Method, path, example JSON response, and headers.
4. **UptimeRobot Setup Recap**
    - URL to use, monitor type, and interval.
5. **How to Verify**
    - How to hit `/api/health` locally.
    - How to confirm UptimeRobot is successfully monitoring the endpoint.