### **Category: Tech Stack & Frameworks**

- Primary Stack: "Use Next.js 14+ (App Router) with TypeScript and Tailwind CSS. strictly avoid the 'Pages' router (`pages/` directory). Do not use Vite."
- Server vs Client: "Default to React Server Components (RSC). Only add `'use client'` at the top of a file if you specifically need React hooks (`useState`, `useEffect`) or browser-only APIs."
- Data Fetching: "Prefer Server Actions for mutations and `fetch` directly in Server Components for data reading. Avoid `useEffect` for data fetching."
- Comments: "Do not add comments that explain what the code does (e.g., 'increments i by 1'). Only comment on why a complex decision was made."

### **Category: Safety & Verification**

- Test First: "Before writing implementation code, always create or update the test plan. Never commit code without verifying that tests pass locally."
- No Placeholders: "Never leave TODOs or placeholder logic (e.g., `pass` or `return true`) in production code unless explicitly instructed."
- Secrets: "Never hardcode API keys or credentials. Always look for `.env` files or environment variables."
- Verification: "After creating a new page or component, always generate a Screenshot artifact of the rendered UI to verify the layout and styling."
- Incremental Changes: "Break large refactors into small, atomic steps. Do not change more than 5 files in a single step without requesting a review."

### **Category: Code Quality & TypeScript**

- Strict Typing: "Do not use `any`. Use `interface` for object definitions. For component props, explicitly define the type (e.g., `interface ButtonProps { ... }`)."
- Routing: "Use the `next/link` component for internal navigation. Ensure all routes used in `Link` components actually exist in the `app/` directory."
- UI Components: "When creating new UI elements, ensure they are responsive using Tailwind utility classes. If a component is complex, break it down into smaller sub-components in a `_components/` folder."

### **Category: Architecture & Tech Stack**

- Hybrid Model: "This project uses a Hybrid Architecture. The Frontend is a Static Export hosted on cPanel. The Backend is a REST API hosted on Render."
- Frontend Constraints: "Strictly DO NOT use React Server Actions (`'use server'`) or Middleware for the frontend logic, as they are not supported in static exports. Use Client Components (`'use client'`) with `fetch` or React Query."
- Backend Communication: "All data fetching must target the external API URL defined in `process.env.NEXT_PUBLIC_API_URL`. Never use relative paths like `/api/health` in the frontend code."

### **Category: Deployment (cPanel & Render)**

- Static Config: "Configure `next.config.js` to use `output: 'export'`. Set `images: { unoptimized: true }` because cPanel cannot process Next.js optimized images."
- Routing: "Set `trailingSlash: true` in the config to ensure cPanel directory routing (e.g., `/about/`) works correctly without 404s."
- Render API: "For the Backend code (API routes), ensure CORS (Cross-Origin Resource Sharing) is enabled to allow requests from the cPanel domain."

### **Category: Code Quality & Analytics**

- Strict Typing: "Strictly avoid using `any` types. If a type is unknown, define a proper interface or use generics."
- SEO Tools: "Use `@next/third-parties` for Google Analytics and Clarity. Load scripts with `strategy='afterInteractive'`."
- Navigation: "Use `<Link>` from `next/link`. Ensure all links point to routes that will exist as static HTML files."

### **Category: Analytics & Integrations**

- Library Preference: "Use the official `@next/third-parties` library for Google Analytics (`<GoogleAnalytics />`) and Google Tag Manager. Do not manually paste `gtag.js` snippets into `layout.tsx` unless necessary."
- Microsoft Clarity: "Integrate Microsoft Clarity using a dedicated Client Component with `next/script`. Set the loading strategy to `strategy='afterInteractive'` to prevent blocking the Largest Contentful Paint (LCP)."
- Env Variables: "Never hardcode Measurement IDs or Project IDs. Always refer to them as `process.env.NEXT_PUBLIC_GA_ID` and `process.env.NEXT_PUBLIC_CLARITY_ID`."