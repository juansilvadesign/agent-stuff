# Global Antigravity Rules – Next.js Hybrid (Concise)

These rules apply to this workspace. Follow them unless explicitly overridden.

---

## 1. Tech Stack & Frameworks

- Use **Next.js 14+ App Router + TypeScript + Tailwind CSS**.
- **Never** use the legacy `pages/` router or Vite.
- Default to **React Server Components**; add `'use client'` only for hooks or browser APIs.
- Comment only on **why** a complex decision was made, not what obvious code does.

---

## 2. Safety, Tests & Verification

- **Test-first**: create/update the test plan before major changes; don’t ship without passing tests.
- Do **not** leave TODOs, stubs, or placeholder logic in production code.
- Never hardcode secrets; always use environment variables / `.env`.
- After creating or updating a page/component, **capture a Screenshot artifact** of the rendered UI.
- Keep changes **incremental**: avoid touching more than **5 files per step** without explicit approval.

---

## 3. Code Quality & TypeScript

- **No `any`**. Always define proper interfaces/types (e.g., `interface ButtonProps { ... }`).
- Use `next/link` for internal navigation; ensure linked routes exist in `app/`.
- Make UI **responsive by default** with Tailwind breakpoints.
- For complex UIs, split into smaller subcomponents in a local `_components/` folder.

---

## 4. Architecture & Data Flow (Hybrid Model)

- Architecture: **Static Next.js frontend on cPanel** + **REST API backend on Render**.
- Frontend is a **static export**:
  - Do **not** use React Server Actions (`'use server'`) or Middleware.
  - Use Client Components with `fetch` or React Query for runtime data fetching.
- All frontend network calls must hit  
  `process.env.NEXT_PUBLIC_API_URL` (no relative `/api/...` paths).

---

## 5. Deployment: cPanel (Frontend) & Render (Backend)

- `next.config.js`:
  - `output: 'export'`
  - `images: { unoptimized: true }`
  - `trailingSlash: true`
- Backend on Render must enable **CORS** for the cPanel domain.

---

## 6. Analytics & Integrations

- Use **`@next/third-parties`** for:
  - Google Analytics (`<GoogleAnalytics />`)
  - Google Tag Manager (if used)
- Use **Microsoft Clarity** via a dedicated Client Component with `next/script`.
- Load analytics/Clarity scripts with `strategy="afterInteractive"` to avoid blocking LCP.
- Never hardcode IDs:
  - Use `process.env.NEXT_PUBLIC_GA_ID`
  - Use `process.env.NEXT_PUBLIC_CLARITY_ID`
  - (and similar env vars for other tools)

