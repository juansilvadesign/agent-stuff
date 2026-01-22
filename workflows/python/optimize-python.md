# Deep Code Audit Workflow

**Goal:** Perform a *deep* audit of the Python codebase focusing on correctness, security, architecture, maintainability, and test health – producing an actionable report before any refactor.

---

## Phase 0: Scope & Context

1. **Identify Scope**
   - Ask the user what to audit:
     - Single file
     - Folder / package
     - Whole repo
   - In Antigravity:
     - If there is a current file or selection, treat it as **primary focus**.
     - Ask explicit permission before expanding to the whole project.

2. **Collect Context**
   - Ask (or infer) and summarize:
     - Project domain (e.g., API, data pipeline, CLI tool, web app).
     - Critical paths / core entrypoints.
     - Non-functional priorities: performance, security, reliability, compliance.
     - Tech stack around Python (DB, message queue, frameworks, etc.).

3. **Baseline Map**
   - Build a short textual map:
     - Main packages/modules.
     - Key entrypoints (`__main__`, CLI, web handlers, tasks).
     - Where tests live (e.g., `tests/`, `*_test.py`, `test_*.py`).

---

## Phase 1: Static & Architectural Audit

1. **Static Scan (Senior Python Rules)**
   - Read the target files as a senior Python engineer.
   - For each module, inspect:
     - Naming, structure, and responsibilities.
     - Use of globals and shared state.
     - Type hints and docstrings.
     - Error handling and logging.

2. **Architecture & Design Review**
   - Identify:
     - Tight coupling between modules.
     - God objects / god modules.
     - Cyclic dependencies and import smells.
     - Violation of clear boundaries (domain vs infrastructure vs UI).
   - Note any missing abstraction layers or over-engineering.

3. **Maintainability & Readability**
   - Flag:
     - Very long functions/methods (> ~40–50 lines).
     - High cyclomatic complexity (deep nesting, many branches).
     - Duplicated logic across files.
     - Inconsistent style vs PEP 8 and project conventions.

4. **Security & Reliability Pass**
   - Look for:
     - Direct use of secrets / hardcoded credentials.
     - Unvalidated external inputs (HTTP, CLI args, env vars, DB).
     - Unsafe eval/exec, shell calls, deserialization, or file access.
     - Missing error handling in I/O, network, DB, and concurrency code.
     - Dangerous default configurations (debug flags, open CORS, etc.).

5. **Issue Log (Pre-Refactor Report)**
   - Before suggesting any code changes, build a **structured issue list**.
   - For each issue, record:

     - **ID:** `AUD-###`
     - **Location:** `path/to/file.py:line` (approximate is fine).
     - **Category:** `Architecture | Maintainability | Security | Correctness | Style | Testability`
     - **Severity:** `info | low | medium | high | critical`
     - **Description:** 1–3 sentences, in plain language.
     - **Evidence:** Short code excerpt or summary.
     - **Recommendation:** Clear, concrete next step (e.g., “Extract helper”, “Add validation”, “Split module”, “Introduce interface”).

   - Present this report *before* moving to any refactor or code rewrite.

---

## Phase 2: Behavioral & Test Audit

1. **Existing Tests Review**
   - Locate and scan tests:
     - What parts of the system are covered?
     - Are tests asserting meaningful behavior or just happy-path?
     - Any flaky patterns (sleep, random, time-dependent, global state)?

2. **Coverage Gaps (Qualitative)**
   - For each critical module / path, note:
     - What is **covered** by tests.
     - What is **not covered** (edge cases, error paths, boundary conditions).
   - Link gaps to specific issues from the Issue Log when relevant.

3. **Test Quality**
   - Flag:
     - Overspecified tests (brittle, tightly coupled to implementation).
     - Underspecified tests (assert almost nothing).
     - Tests that mix many concerns into one function.
   - Suggest improvements in structure:
     - Use of fixtures, parametrization, factory functions, or mocks.

4. **Test Proposals**
   - Propose at least **3–5 high-value tests**, including:
     - 1–2 for happy-path behavior of core flows.
     - 1–2 for edge cases / error handling.
     - 1 for security or robustness (invalid/corrupt data, unexpected types).
   - Provide pytest-style skeletons (without actually modifying files unless requested).

---

## Phase 3: Targeted Deep Dives

1. **Critical Path Deep Dive**
   - Identify the top **2–3 most critical flows** (by business impact or risk).
   - For each:
     - Trace the call chain (from entrypoint to lowest-level functions).
     - Look for:
       - Hidden side effects and shared state.
       - Inconsistent assumptions between layers.
       - Error paths that silently fail or swallow exceptions.
       - Misaligned types and contracts between functions.

2. **Security / Data Integrity Deep Dive** (if relevant)
   - For code touching:
     - Authentication / authorization.
     - Payment or sensitive data.
     - External integrations (webhooks, APIs, message queues).
   - Check:
     - Validation, sanitization, and normalization of inputs.
     - Logging practices (no sensitive data in logs).
     - Clear separation of trust boundaries.

3. **Concurrency & Resource Management (when present)**
   - For async code, threads, or multiprocessing:
     - Look for race conditions, deadlocks, shared mutable state.
     - Ensure proper handling of cancellations, timeouts, and retries.
   - For resources (files, sockets, DB connections):
     - Confirm use of context managers or equivalent cleanup.
     - Flag any potential leaks.

---

## Phase 4: Recommendations & Roadmap (No Blind Refactors)

1. **Prioritize Issues**
   - From the Issue Log, build a **prioritized roadmap**:
     - Group by category (Architecture, Security, Maintainability, Tests).
     - Within each group, sort by: `Severity × Blast Radius × Effort`.
   - Create 3 buckets:
     - **Quick Wins (low effort, high impact)**
     - **Medium Refactors**
     - **Strategic Refactors / Redesign**

2. **Concrete Recommendations**
   - For each top issue or group of related issues, provide:
     - **Current State:** what’s wrong and why it matters.
     - **Target State:** what “good” looks like.
     - **Suggested Change:** high-level refactor plan (no huge rewrites).
     - **Risk Notes:** potential regressions to watch out for.

3. **Testing Plan Alignment**
   - Map recommendations to tests:
     - Which new tests should be added before refactoring.
     - Which existing tests may need updates.
     - Which flows must be re-verified after changes.

4. **Final Deliverables**
   - Output the audit as:
     1. **Executive Summary (non-technical):**
        - 5–10 bullet points explaining main risks and opportunities.
     2. **Technical Appendix:**
        - Full Issue Log.
        - Test coverage & gap notes.
        - Deep dive findings.
        - Recommended roadmap.

---

## Constraints & Safety

- **No Automatic Large-Scale Rewrites:**
  - Do **not** propose or apply “big bang” rewrites.
  - Prefer incremental, test-driven changes.

- **Behavioral Stability:**
  - Assume that external APIs and public interfaces must remain stable unless the user explicitly approves breaking changes.

- **Transparency:**
  - If a judgment is based on inference (not explicit evidence in code), mark it as **Assumption** and highlight which information would reduce uncertainty.

- **Ask Before Refactoring:**
  - After presenting the full audit report, stop and ask:
    > “Do you want me to help design refactors and code changes based on this roadmap, or keep it as an audit only?”
