You are an expert backend/Next.js engineer working on a Render-hosted API that must be safely consumed by a static frontend (e.g. on cPanel).

Your job is to **configure CORS headers** so the static frontend can fetch from the Render API, using Next.js `next.config.js` when possible.

Follow this strict workflow:

STEPS (DO IN ORDER)
1) Identify Config
2) Add Headers
3) Configure Rules
4) Verify

---

## 🔑 INPUTS

- Development allowed origin (optional): {{dev_origin}}
  e.g. `http://localhost:3000`
- Production allowed origin (cPanel URL): {{prod_origin}}
  e.g. `https://frontend.yourdomain.com`
- API base path (default `/api`): {{api_base_path}}
- Is this a Next.js app with App/Pages API routes? (if unclear, infer from repo): {{using_next_api}}

If some fields are missing, infer them from the repo and **explicitly state your assumptions** before applying changes.

---

## ⚙️ GLOBAL RULES

- Prefer configuring CORS centrally using `next.config.js` when Next API routes are used.
- Apply headers to **API routes only** (e.g. `/api/:path*`), not to static assets or pages.
- Avoid hard-coding origins directly in code if environment variables are already used elsewhere; prefer e.g. `process.env.CORS_ORIGIN`.
- Keep behavior safe:
  - In production, avoid `Access-Control-Allow-Origin: *` when credentials are allowed.
  - For development, a wildcard may be acceptable if no secrets/credentials are sent.
- At the end, provide a simple test script/snippet for verifying headers from the static frontend.

---

## 1️⃣ STEP 1 — IDENTIFY CONFIG (next.config.js or Server Entry)

Goal: Locate where CORS should be configured.

1. Check for a `next.config.js` or `next.config.mjs` file in the project root.
2. Inspect the project structure to confirm:
   - Using Next.js API routes: `pages/api/` or `app/api/` are present.
3. If **Next.js is used with API routes**:
   - Plan to add an `async headers()` function in `next.config.js`.
4. If **no Next.js API routes** and a custom server is used (e.g. Express/Fastify):
   - Identify the main server entry (e.g. `server.js`, `src/server.ts`, or `index.js`).
   - Plan to configure CORS via middleware for routes under `{{api_base_path}}` (default `/api`).

**Output for this step:**

- State where CORS will be configured:
  - `next.config.js` (preferred), or
  - Main server file (name + path).
- Confirm the API base path to be used for CORS rules.

Then continue to STEP 2 automatically.

---

## 2️⃣ STEP 2 — ADD HEADERS (next.config.js `headers()`)

Goal: Implement an `async headers()` function to attach CORS headers to API routes.

If using **Next.js** with `next.config.js`:

1. Open or create `next.config.js` (or `.mjs`) in the project root.
2. Ensure there is an exported config object and add an `async headers()` function:

   ```js
   /** @type {import('next').NextConfig} */
   const nextConfig = {
     async headers() {
       return [
         {
           // Apply these headers to all API routes
           source: '/api/:path*',
           headers: [
             {
               key: 'Access-Control-Allow-Credentials',
               value: 'true',
             },
             {
               key: 'Access-Control-Allow-Origin',
               // In production, prefer a strict origin:
               // e.g. 'https://your-cpanel-domain.com'
               value: process.env.CORS_ORIGIN || '*',
             },
             {
               key: 'Access-Control-Allow-Methods',
               value: 'GET,DELETE,PATCH,POST,PUT',
             },
             {
               key: 'Access-Control-Allow-Headers',
               value:
                 'X-CSRF-Token, X-Requested-With, Accept, Accept-Version, Content-Length, Content-MD5, Content-Type, Date, X-Api-Version',
             },
           ],
         },
       ];
     },
   };

   module.exports = nextConfig;
   ```
3. If a `headers()` function already exists:
    - Merge the CORS configuration into the existing headers.
    - Make sure you **don’t override** other required headers.

If using a **custom server**:

- Configure an equivalent set of headers via middleware on `{{api_base_path}}`, mirroring the header values above.

**Output for this step:**

- Show the final `next.config.js` (or relevant snippet for the custom server).
- Explicitly indicate what value is used for `Access-Control-Allow-Origin` in:
    - Development
    - Production

Then continue to STEP 3 automatically.

---

## 3️⃣ STEP 3 — CONFIGURE RULES (Per Requirements)

Goal: Ensure all required CORS headers are correctly configured for API routes.

Verify that the configuration includes the following **exact headers** for API routes:

- `Access-Control-Allow-Credentials`: `true`
- `Access-Control-Allow-Origin`:
    - For production, set to the **static frontend URL** (e.g. your cPanel domain).
    - For local/dev,  or `http://localhost:xxxx` is acceptable depending on security needs.
- `Access-Control-Allow-Methods`: `GET,DELETE,PATCH,POST,PUT`
- `Access-Control-Allow-Headers`:
    
    `X-CSRF-Token, X-Requested-With, Accept, Accept-Version, Content-Length, Content-MD5, Content-Type, Date, X-Api-Version`
    

Actions:

1. If `process.env.CORS_ORIGIN` or similar env vars are available:
    - Document their expected values (dev vs prod).
2. If not, consider adding them to `.env.example` (but do not leak real secrets).

**Output for this step:**

- A bullet list confirming each required header is present and correctly configured.
- If environment variables are used, describe how they should be set (e.g. `CORS_ORIGIN=https://frontend.example.com`).

Then continue to STEP 4 automatically.

---

## 4️⃣ STEP 4 — VERIFY (Test Fetch Script)

Goal: Confirm the static frontend can see the CORS headers.

1. Create a simple test fetch script, either:
    - A standalone Node script (e.g. `scripts/test-cors.js`), **or**
    - A small HTML + JS snippet that can run from the frontend environment.

**Example Node script (`scripts/test-cors.js`):**

```jsx
// scripts/test-cors.js
const fetch = (...args) => import('node-fetch').then(({ default: f }) => f(...args));

async function testCors() {
  const url = process.env.TEST_API_URL || 'https://your-render-api.onrender.com/api/health';

  console.log('Testing CORS against:', url);

  const res = await fetch(url, {
    method: 'GET',
  });

  console.log('Status:', res.status);
  console.log('Access-Control-Allow-Origin:', res.headers.get('access-control-allow-origin'));
  console.log('Access-Control-Allow-Credentials:', res.headers.get('access-control-allow-credentials'));
  console.log('Access-Control-Allow-Methods:', res.headers.get('access-control-allow-methods'));
  console.log('Access-Control-Allow-Headers:', res.headers.get('access-control-allow-headers'));

  const body = await res.json().catch(() => null);
  console.log('Body:', body);
}

testCors().catch(console.error);

```

1. Or, from your static frontend (e.g. cPanel-hosted HTML/JS), use:

```html
<script>
  async function testCors() {
    const res = await fetch('https://YOUR-RENDER-API.onrender.com/api/health', {
      method: 'GET',
      credentials: 'include', // if you intend to send cookies
    });

    console.log('Status:', res.status);
    console.log('Origin:', window.location.origin);
    console.log('Body:', await res.json());
  }

  testCors();
</script>

```

1. Run the test and confirm:
    - The request succeeds (no CORS errors in browser console).
    - The response includes the expected CORS headers.
    - `Access-Control-Allow-Origin` matches your static frontend origin in production.

**Output for this step:**

- Which test method was used (Node script or browser).
- Observed values for:
    - Status code
    - `Access-Control-Allow-Origin`
    - `Access-Control-Allow-Credentials`
- Whether the test indicates CORS is correctly configured.

---

## ✅ FINAL OUTPUT FORMAT

At the very end, respond with:

1. **Summary**
    - 3–5 bullets describing what was configured (where, how, and with which origins).
2. **Files Created/Modified**
    - `next.config.js` (or server entry)
    - Any test script file (e.g. `scripts/test-cors.js`)
3. **CORS Rules**
    - List of final header values used for dev and prod.
4. **How to Test**
    - Commands or steps to run the test script or use the browser to verify.
5. **Notes/Follow-ups**
    - Any recommendations (e.g. locking down `Access-Control-Allow-Origin` in production, handling OPTIONS/preflight if needed).