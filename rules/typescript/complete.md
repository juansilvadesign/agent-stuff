# Antigravity Rules – Next.js Hybrid (cPanel + Render)

These rules apply to any Antigravity Agent working in this workspace.  
Follow them **strictly** unless the user explicitly overrides a rule.

---

## 1. Tech Stack & Frameworks

1.1 **Primary Stack**

- Always assume the frontend uses **Next.js 14+ with the App Router**, **TypeScript**, and **Tailwind CSS**.
- **Never** create or modify code in the legacy `pages/` router. Do not introduce a `pages/` directory.
- **Do not** scaffold or configure projects with Vite. Use Next.js tooling only.

1.2 **Server vs Client Components**

- Default to **React Server Components (RSC)** for shared layout and non-interactive UI.
- Only add `'use client'` at the top of a file when:
  - You need React hooks (`useState`, `useEffect`, `useMemo`, etc.), or
  - You need browser-only APIs (DOM, `window`, `document`, `localStorage`, etc.), or
  - You are implementing analytics, Clarity, or other client-only scripts.
- When possible, keep leaf components as server components to reduce bundle size.

1.3 **Data Fetching**

- **General principle:** Avoid using `useEffect` for low-level data fetching. Prefer data libraries or framework primitives.
- For this project (static frontend + external REST API):
  - **Do NOT use React Server Actions** (`'use server'`) anywhere.
  - Perform **all runtime data fetching from client components** using:
    - `fetch` inside an abstraction (e.g., API utilities), or
    - React Query / TanStack Query hooks.
- Do not introduce ad-hoc `useEffect` + `fetch` patterns when React Query (or equivalent) is available.

1.4 **Comments**

- Do **not** add comments that restate what the code obviously does (e.g., `// increments i by 1`).
- Only comment when:
  - Explaining **why** a non-obvious decision was taken,
  - Documenting trade-offs, constraints, or workarounds,
  - Clarifying assumptions that are not obvious from the code.

---

## 2. Safety, Testing & Verification

2.1 **Test-First Mindset**

- Before writing or refactoring implementation code:
  - Create or update a **test plan** (unit, integration, or E2E as appropriate).
  - Where tests exist, extend or adjust them first when reasonable.
- Never consider a task “done” until you have:
  - Run the test suite locally, and
  - Verified that all relevant tests pass.

2.2 **No Placeholders in Production**

- Do **not** leave TODOs, `pass`, `return true`, mock implementations, or commented-out pseudo-code in production logic unless the user explicitly asks for a stub.
- If you must leave follow-up work:
  - Use a clear, searchable marker in non-production files (e.g., docs or planning artifacts), not in core runtime code.

2.3 **Secrets & Credentials**

- Never hardcode API keys, tokens, or credentials.
- Always read secrets from environment variables or `.env` files.
- If you see hardcoded secrets, immediately:
  - Flag the issue in your summary, and
  - Propose a migration plan to environment variables.

2.4 **Verification with Artifacts**

- After creating or modifying a page or significant UI component:
  - Run the app locally.
  - Open the relevant route in the browser.
  - **Generate a Screenshot artifact** of the rendered UI to verify layout and styling.
- When you perform non-trivial backend-related work:
  - Include logs, command output, or small test snippets as artifacts when helpful.

2.5 **Incremental Changes**

- Break large refactors into **small, atomic steps**.
- In a single step:
  - Do not modify more than **5 files** unless the user explicitly approves a larger change.
- Before starting the next step in a large refactor:
  - Summarize what changed,
  - Confirm tests are green,
  - Request review if the impact is significant.

---

## 3. Architecture & Tech Stack (Hybrid Model)

3.1 **Overall Architecture**

- Treat the system as a **Hybrid Architecture**:
  - **Frontend:** Next.js static export hosted on **cPanel**.
  - **Backend:** REST API hosted on **Render**.
- Assume the backend is the **source of truth** for dynamic data and side effects.

3.2 **Frontend Constraints (Static Export)**

- Configure and maintain the frontend to support **static export**; do not rely on server runtime features in Next.js.
- **Strictly DO NOT**:
  - Use React Server Actions (`'use server'`),
  - Use Next.js Middleware for runtime logic,
  - Introduce features that require Edge or Node runtime at request time.
- When in doubt, prefer:
  - Static generation of markup, and
  - Client-side data fetching from the external API.

3.3 **Backend Communication**

- All data fetching from the frontend must target the external API URL defined in:
  - `process.env.NEXT_PUBLIC_API_URL`
- Do **not** call relative API paths like `/api/health`, `/api/users`, etc., from frontend code.
- When implementing API utilities:
  - Always build URLs by prefixing with `process.env.NEXT_PUBLIC_API_URL`.
  - Centralize HTTP client logic (e.g., `lib/api.ts`) instead of scattering raw `fetch` calls.

---

## 4. Deployment: cPanel (Frontend) & Render (Backend)

4.1 **Next.js Export Configuration**

- In `next.config.js`:
  - Set `output: 'export'`.
  - Set `images: { unoptimized: true }` because cPanel does not handle Next’s image optimization pipeline.
- Ensure that new features remain compatible with static export (no dynamic `app` routes that require a server).

4.2 **Routing for cPanel**

- Set `trailingSlash: true` in `next.config.js` so that routes like `/about/` resolve correctly on cPanel.
- When adding new routes:
  - Ensure the static HTML generated by `next export` matches the trailing slash pattern.

4.3 **Backend (Render) & CORS**

- When editing or proposing backend code:
  - Ensure **CORS is enabled** to allow requests from the cPanel domain(s).
- Explicitly list allowed origins and headers where appropriate.
- Document any CORS decisions (e.g., wide vs restricted origins) in code comments or docs.

---

## 5. Code Quality & TypeScript

5.1 **Strict Typing**

- Strictly avoid `any`. Do not introduce new `any` types.
- Use `interface` (or `type` when appropriate) for object shapes.
- For React components:
  - Always define explicit prop types:
    - Example: `interface ButtonProps { label: string; onClick?: () => void }`.
- If a type is unknown:
  - Define a proper interface,
  - Or use generics with constraints,
  - Or introduce a temporary **narrow** type with a clear TODO explaining how to refine it (outside of core runtime paths).

5.2 **Structure & Decomposition**

- Keep components small and focused.
- For complex UI components:
  - Extract subcomponents into a `_components/` folder within the feature or route directory.
  - Avoid monolithic components that handle multiple responsibilities.

5.3 **Tailwind & Responsiveness**

- Ensure all new UI elements are **responsive by default** using Tailwind utility classes.
- Handle at least:
  - Mobile (`sm:`), tablet (`md:`), and desktop (`lg:`/`xl:`) breakpoints where relevant.
- Prefer composition of utility classes over ad-hoc inline styles.

---

## 6. Routing & Navigation

6.1 **Next.js Routing**

- Use the **App Router** (`app/` directory) exclusively.
- For internal navigation:
  - Always use `<Link>` from `next/link` instead of plain `<a>` tags (except for external URLs).
- Before using a route in `<Link href="...">`:
  - Ensure that a corresponding path exists or will exist as a static HTML route in the `app/` directory.
  - Keep route naming consistent and predictable.

6.2 **Static Routes**

- Design routes to work as static HTML under `next export`:
  - Avoid dynamic segments that cannot be pre-rendered without additional configuration or data.
  - If dynamic segments are necessary, confirm that they can still be statically generated or handled by the backend.

---

## 7. Analytics & Integrations

7.1 **Analytics Libraries**

- Use the official `@next/third-parties` package for:
  - **Google Analytics** (`<GoogleAnalytics />`),
  - **Google Tag Manager**, where applicable.
- Do **not** manually paste `gtag.js` or raw script snippets into `layout.tsx` unless there is a strong, documented reason.

7.2 **Script Loading Strategy**

- For analytics and tracking scripts:
  - Load them with `strategy='afterInteractive'` to avoid blocking render and compromising LCP.
- Ensure scripts are included only in client-appropriate components or layouts where necessary.

7.3 **Microsoft Clarity**

- Integrate Microsoft Clarity via a **dedicated Client Component** that uses `next/script`.
- Always set `strategy='afterInteractive'` for Clarity.
- Avoid mixing Clarity logic with unrelated UI concerns; keep the tracking component isolated.

7.4 **Environment Variables for IDs**

- Never hardcode:
  - Google Analytics Measurement IDs,
  - Tag Manager IDs,
  - Microsoft Clarity Project IDs.
- Always read them from:
  - `process.env.NEXT_PUBLIC_GA_ID`
  - `process.env.NEXT_PUBLIC_CLARITY_ID`
  - Or similarly named, documented environment variables.
- If an ID appears hardcoded, flag it and propose migrating it to an env variable.

---

## 8. Agent Behavior in Antigravity

8.1 **Planning & Communication**

- For non-trivial tasks:
  - Start by creating a brief plan (as an artifact or inline summary) before editing files.
- Clearly communicate:
  - What you plan to change,
  - Which files will be touched (aim for ≤ 5 per step),
  - How you will test and verify the changes.

8.2 **Use of Tools**

- Prefer editing only the files necessary to complete the current task.
- Use the terminal to:
  - Install dependencies,
  - Run builds,
  - Run tests and linters.
- Use the browser to:
  - Verify routes and UIs,
  - Capture Screenshot artifacts after UI changes.

8.3 **User Reviews**

- When a change affects many files, core architecture, or critical flows:
  - Pause and request user review with a clear summary before proceeding.
- Treat the user as the final authority:
  - If user instructions conflict with these rules, follow the user **after** confirming they understand the trade-offs.

---

## 9. Conflict Resolution

- If you detect conflicting requirements between these rules and a user request:
  1. Call out the conflict explicitly.
  2. Propose a safe, standards-aligned solution.
  3. If the user insists on a risky choice (e.g., disabling CORS restrictions, hardcoding secrets), document the risk in your summary and implement the request minimally and carefully.

These rules are persistent. Always re-read them when starting work in this workspace and follow them unless explicitly instructed otherwise.
