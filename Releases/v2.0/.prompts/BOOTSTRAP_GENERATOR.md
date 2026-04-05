# BOOTSTRAP_GENERATOR.md
<!-- Version: 2.0 -->

> **What this file does:** Generates a comprehensive `docs/BOOTSTRAP_PROMPT.md` for deep project onboarding. This is the file you paste at the start of a new Claude Code session to give Claude deep architectural understanding.
>
> **How to run:** Prompt Claude Code with: `"Read .prompts/BOOTSTRAP_GENERATOR.md and create docs/BOOTSTRAP_PROMPT.md"`
>
> **Pre-requisite:** Run PROJECT_SURVEY.md first. The survey output must be in context.
>
> **Output:** Creates `docs/BOOTSTRAP_PROMPT.md` (renames existing to `BOOTSTRAP_PROMPT_old.md` if present).

---

## Instructions for Claude Code

You are about to generate `docs/BOOTSTRAP_PROMPT.md` for this project. This file provides deep architectural understanding — it's what transforms Claude from "generic assistant" to "team member who knows the codebase."

**This file should be:**

1. **Comprehensive** — 100-200 lines, covers architecture, patterns, and quirks
2. **Pedagogical** — Ordered reading list, progressive understanding
3. **Specific** — Real file paths, real quirks with file:line references
4. **Non-duplicative** — Rules and commands live in CLAUDE.md, not here

**Use the PROJECT_SURVEY_OUTPUT.md** (already in context) as your source of truth.

---

## File Handling

Before creating the new file:

1. Ensure `docs/` directory exists (create if not)
2. Check if `docs/BOOTSTRAP_PROMPT.md` exists
3. If yes, rename it to `BOOTSTRAP_PROMPT_old.md`
4. If `BOOTSTRAP_PROMPT_old.md` already exists, rename to `BOOTSTRAP_PROMPT_old2.md` (and so on)
5. Create the new `docs/BOOTSTRAP_PROMPT.md`

---

## Document Structure

Generate `BOOTSTRAP_PROMPT.md` with the following sections. **Omit any section that doesn't apply.**

### Section 1: Header with Instructions

```markdown
# Bootstrap Prompt — [Project Name]

> **How to use:** Copy and paste this entire file into Claude Code at the start of a new session, after Claude has read CLAUDE.md. This gives Claude deep understanding of the project architecture.
>
> **Last updated:** [DATE]
```

---

### Section 2: Ordered Reading List

A curated list of files Claude should read to understand the project. Order matters — foundational files first.

```markdown
## Reading List

Read these files in order to understand the project:

### Mandatory (read all)

1. `CLAUDE.md` — Project rules, commands, conventions (Claude reads automatically)
2. `src/index.ts` — Application entry point
3. `src/db/schema.ts` — Database schema, the source of truth for data
4. `src/api/routes.ts` — All API endpoints
5. `src/llm/client.ts` — LLM integration patterns
6. `drizzle.config.ts` — Database configuration
7. `astro.config.mjs` — Framework configuration
8. `.env.example` — Required environment variables

### Optional (for deep dives)

- `src/pdf/parser.ts` — PDF extraction logic (read if working on PDF features)
- `src/auth/config.ts` — Auth configuration (read if working on auth)
- `docs/ARCHITECTURE.md` — Detailed system design (read for major changes)
- `docs/LLM.md` — LLM quirks and patterns (read if working on LLM features)
```

**Guidelines for reading list:**
- 6-10 mandatory files
- Order from general to specific
- Include config files that reveal architecture
- Optional files grouped by feature area

---

### Section 3: What This Project Is

Brief orientation — what someone needs to know in 30 seconds.

```markdown
## What This Project Is

[Project Name] is a [type of application] that [primary function]. It's built for [target user/use case] and [key distinguishing feature].

**Key characteristics:**
- [Characteristic 1 — e.g., "Runs entirely local, no cloud dependencies"]
- [Characteristic 2 — e.g., "Multi-machine: dev on Mac, LLM inference on separate GPU server"]
- [Characteristic 3 — e.g., "Single user, deadline-driven development"]
```

Keep to 2-3 sentences plus 3-5 bullet points.

---

### Section 4: Infrastructure Diagram (If Multi-Machine)

ASCII box diagram showing machines, services, and connections. Only include if the project spans multiple machines or has non-trivial infrastructure.

```markdown
## Infrastructure

```
┌─────────────────────────────────────────────────────────────┐
│                     Mac Mini M4 (Dev)                       │
│                      192.168.1.59                           │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │ Astro App   │  │  Docker     │  │  VS Code / Claude   │ │
│  │ localhost:  │  │  Postgres   │  │  Code               │ │
│  │ 4321        │  │  :5432      │  │                     │ │
│  └──────┬──────┘  └─────────────┘  └─────────────────────┘ │
│         │                                                   │
└─────────┼───────────────────────────────────────────────────┘
          │ HTTP API (OpenAI-compatible)
          ▼
┌─────────────────────────────────────────────────────────────┐
│                   RTX 3090 PC (LLM)                         │
│                    192.168.1.56                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                   LM Studio                          │   │
│  │                   :1234                              │   │
│  │  ┌─────────────┐  ┌─────────────┐                   │   │
│  │  │ qwen3-8b    │  │ qwen2.5-vl  │  (one at a time)  │   │
│  │  └─────────────┘  └─────────────┘                   │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

**Network notes:**
- LLM API has no auth — security is LAN isolation
- First request per model: 10-30s (JIT loading)
- Models auto-evict when switching
```

---

### Section 5: Architecture in Brief

One-line data flow plus key architectural patterns.

```markdown
## Architecture

**Data flow:** User uploads PDF → parsed to images/text → sent to LLM for extraction → structured data stored in Postgres → displayed in UI

**Key patterns:**

1. **Module isolation** — each domain (db, llm, auth, pdf) is self-contained with explicit exports
2. **API response shape** — all endpoints return `{ success, data?, error? }`
3. **Validation at boundaries** — Zod schemas validate all inputs at API layer
4. **Error handling** — errors logged with Pino, user sees friendly messages
5. **No global state** — all state in database or request context
```

---

### Section 6: Module Boundaries Table

Clear ownership table. This is the "never cross these lines" reference.

```markdown
## Module Boundaries

| Module | Owns | Exports | Never Import Here |
|--------|------|---------|-------------------|
| `src/db/` | All database access | Query functions, types | Raw SQL, other ORMs |
| `src/llm/` | All LLM communication | `extractData()`, `generateSchema()` | Direct API calls |
| `src/auth/` | Authentication & sessions | `getSession()`, `requireAuth()` | Session storage details |
| `src/pdf/` | PDF parsing & image extraction | `parsePDF()`, `extractImages()` | pdfjs internals |
| `src/api/` | HTTP request handling | Route handlers | Business logic |
| `src/schemas/` | Zod validation schemas | All schema exports | Runtime validation |
| `src/utils/` | Shared utilities | Pure helper functions | Domain logic |

**Rule:** Import from a module's public API (index.ts or explicit exports), never reach into internal files.
```

---

### Section 7: Key Files and What They Do

10-20 most important files with their purpose and key exports.

```markdown
## Key Files

| File | Purpose | Key Exports / Notes |
|------|---------|---------------------|
| `src/index.ts` | App entry point | Starts Astro server |
| `src/db/schema.ts` | Database schema | All table definitions |
| `src/db/queries.ts` | Database queries | `getDocuments()`, `saveExtraction()` |
| `src/llm/client.ts` | LLM client wrapper | `createCompletion()`, handles retries |
| `src/llm/prompts.ts` | Prompt templates | `EXTRACTION_PROMPT`, `SCHEMA_PROMPT` |
| `src/pdf/parser.ts` | PDF processing | `parsePDF()` → images + text |
| `src/auth/config.ts` | Better Auth setup | Auth configuration |
| `src/api/upload.ts` | File upload handler | POST /api/upload |
| `src/api/extract.ts` | Extraction endpoint | POST /api/extract |
| `src/schemas/document.ts` | Document schemas | `DocumentSchema`, `ExtractionSchema` |
| `src/pages/index.astro` | Home page | Main UI |
| `src/components/UploadForm.tsx` | Upload component | React island for uploads |
| `drizzle.config.ts` | Drizzle ORM config | Database connection |
| `astro.config.mjs` | Astro config | SSR, integrations |
| `docker-compose.yml` | Docker services | PostgreSQL setup |
```

---

### Section 8: Quirks That Have Caused Bugs

Numbered list of specific issues with file:line references where possible. These are hard-won lessons.

```markdown
## Quirks & Gotchas

These have caused bugs. Learn from our pain:

1. **Qwen 3 "thinks" by default** (`src/llm/client.ts:47`)
   - Append `/no_think` to prompts for simple extractions
   - Without it, you get chain-of-thought reasoning in the output
   - Example: `"Extract the date/no_think"` not `"Extract the date"`

2. **First LLM request is slow** (`src/llm/client.ts:23`)
   - JIT model loading takes 10-30 seconds
   - Subsequent requests are <2 seconds
   - Don't assume timeout on first request = broken

3. **HTMX file uploads break with hx-boost** (`src/components/UploadForm.tsx:12`)
   - Must add `hx-boost="false"` to forms with file inputs
   - Otherwise multipart/form-data gets mangled

4. **Drizzle migrations are append-only** (`drizzle/`)
   - Never edit existing migration files
   - Always create new migrations for changes
   - Editing existing = database drift

5. **pdfjs worker must be copied** (`astro.config.mjs:15`)
   - Worker file must exist in `public/pdf.worker.js`
   - Build script copies it, but manual runs may miss this

6. **PyObjC import order matters** (`src/browser/controller.py:1-5`)
   - Must import `AppKit` before `ApplicationServices`
   - Wrong order = cryptic import errors

7. **Brave bundle ID differs from Chrome** (`src/browser/detector.py:34`)
   - Brave: `com.brave.Browser`
   - Chrome: `com.google.Chrome`
   - Detection code must handle both

8. **Environment variables in Astro** (`src/env.d.ts`)
   - Use `import.meta.env.VARIABLE` not `process.env`
   - Must be prefixed with `PUBLIC_` to expose to client
```

---

### Section 9: Platform/Framework-Specific Gotchas

Separate section for platform-level issues (not project-specific bugs).

```markdown
## Platform Gotchas

### macOS
- Accessibility permissions required for browser automation
- First `git push` per session may trigger Keychain prompt
- Docker Desktop must be running for PostgreSQL

### Astro
- SSR mode requires `output: 'server'` in config
- API routes go in `src/pages/api/`, not `src/api/`
- Islands (React components) need `client:load` directive

### Chrome MV3 Extensions
- Service workers have no persistent state
- Use `chrome.storage` for persistence
- Background scripts terminate after 30s idle
- Content scripts can't access `chrome.*` APIs directly

### Drizzle ORM
- Schema changes require migration generation
- `db push` for dev, `db migrate` for production
- Introspection with `db pull` if schema exists
```

---

### Section 10: Testing Conventions

How tests are organized and run.

```markdown
## Testing

- **Framework:** bun:test
- **Location:** `tests/` mirrors `src/` structure
- **Run all:** `just test` or `bun test`
- **Watch mode:** `just test-watch`
- **Coverage:** `just test-cov`

**Patterns:**
- Unit tests for pure functions in `utils/`
- Integration tests for API endpoints
- LLM tests use mocked responses (see `tests/mocks/llm.ts`)
- Database tests use test database, reset between runs

**Current count:** ~45 tests, ~80% coverage on core modules
```

---

### Section 11: Essential Commands

Brief command reference — full list is in CLAUDE.md.

```markdown
## Commands (Quick Reference)

```bash
just dev          # Start dev server
just test         # Run tests
just db-up        # Start database
just db-migrate   # Run migrations
just lint         # Lint & format
just build        # Production build
```

See `CLAUDE.md` for the complete command reference.
```

---

### Section 12: How to Work in This Project

Numbered rules for how to approach work. These are workflow guidelines.

```markdown
## How to Work Here

1. **Start by reading** — CLAUDE.md (auto), then paste this file, then SESSION_HANDOFF.md
2. **Check existing patterns** — before creating something new, find 2-3 similar examples in the codebase
3. **Stay in your module** — if you need something from another module, use its exports
4. **Test locally first** — especially LLM features, don't assume they work
5. **Commit often** — small commits on master, no feature branches
6. **Update docs** — run maintenance prompts before committing if architecture changed
7. **Ask about quirks** — if something behaves unexpectedly, check the Quirks section first
8. **Keep CLAUDE.md lean** — deep explanations go here, rules go there
```

---

## Quality Checklist

Before presenting the generated BOOTSTRAP_PROMPT.md, verify:

- [ ] **100-200 lines** — comprehensive but not bloated
- [ ] **Reading list is ordered** — foundational files first
- [ ] **Module boundaries are clear** — every major module has a row
- [ ] **Quirks are specific** — file:line references where possible
- [ ] **Infrastructure diagram is accurate** — if multi-machine
- [ ] **No duplicate content from CLAUDE.md** — rules and commands stay there
- [ ] **Key files actually exist** — verify paths are correct
- [ ] **Testing section reflects reality** — correct framework, location, count

---

## Examples

### Example 1: Python CLI Project (~100 lines)

```markdown
# Bootstrap Prompt — url-shortener

> **How to use:** Copy and paste this entire file into Claude Code at the start of a new session.
>
> **Last updated:** 2026-04-01

## Reading List

### Mandatory
1. `CLAUDE.md` — Rules and commands
2. `src/cli.py` — Entry point, argument parsing
3. `src/shortener.py` — Core shortening logic
4. `src/api_client.py` — is.gd API integration
5. `pyproject.toml` — Dependencies and config

### Optional
- `tests/test_shortener.py` — Test patterns

## What This Project Is

url-shortener is a CLI tool that shortens URLs using the is.gd API. It's designed for quick terminal use with clipboard integration.

**Key characteristics:**
- Single file output, pip-installable
- No API key required (is.gd is free)
- Copies result to clipboard automatically on macOS

## Architecture

**Data flow:** CLI args → validate URL → call is.gd API → return/copy shortened URL

**Key patterns:**
1. Click for CLI argument parsing
2. httpx for HTTP requests
3. pyperclip for clipboard (optional dependency)

## Module Boundaries

| Module | Owns | Exports |
|--------|------|---------|
| `src/cli.py` | Argument parsing, output | `main()` |
| `src/shortener.py` | URL validation, orchestration | `shorten_url()` |
| `src/api_client.py` | is.gd API communication | `IsGdClient` |

## Key Files

| File | Purpose |
|------|---------|
| `src/cli.py` | CLI entry point |
| `src/shortener.py` | Core logic |
| `src/api_client.py` | API client |
| `pyproject.toml` | Project config |

## Quirks

1. **is.gd rate limits** (`src/api_client.py:15`) — max 10 requests/minute from same IP
2. **pyperclip fails silently** (`src/cli.py:34`) — clipboard copy is best-effort, don't fail if unavailable

## Testing

- **Framework:** pytest
- **Location:** `tests/`
- **Run:** `uv run pytest`
- **Count:** 12 tests

## How to Work Here

1. Read CLAUDE.md, then this file
2. Core logic in `shortener.py`, CLI concerns in `cli.py`
3. Test with `uv run pytest` before committing
```

---

### Example 2: Browser Automation (~150 lines)

```markdown
# Bootstrap Prompt — browser-automation

> **How to use:** Copy and paste this entire file into Claude Code at the start of a new session.
>
> **Last updated:** 2026-04-01

## Reading List

### Mandatory
1. `CLAUDE.md` — Rules and commands
2. `src/orchestrator/controller.py` — Main browser controller
3. `src/orchestrator/accessibility.py` — macOS Accessibility API wrapper
4. `extension/background.js` — Extension service worker
5. `extension/content.js` — DOM capture script
6. `extension/manifest.json` — MV3 manifest

### Optional
- `src/utils/dom_to_markdown.py` — DOM conversion logic
- `docs/ARCHITECTURE.md` — Detailed flow diagrams

## What This Project Is

browser-automation captures any web page's DOM as markdown by orchestrating a real Brave browser. It uses macOS Accessibility API for browser control (undetectable — no CDP, no Playwright) and a Chrome MV3 extension for DOM access.

**Key characteristics:**
- Fully undetectable — no browser automation signatures
- macOS only (requires Accessibility API)
- Brave browser only (Chrome untested)
- Two-part system: Python orchestrator + browser extension

## Infrastructure

```
┌─────────────────────────────────────────────┐
│              macOS Dev Machine              │
│  ┌─────────────┐      ┌─────────────────┐  │
│  │   Python    │      │     Brave       │  │
│  │ Orchestrator│◄────►│   + Extension   │  │
│  │             │ A11y │                 │  │
│  │             │ API  │                 │  │
│  └─────────────┘      └─────────────────┘  │
└─────────────────────────────────────────────┘
```

Communication: Python → Accessibility API → Brave window → Extension (native messaging)

## Architecture

**Data flow:** Python requests page → A11y API activates Brave → Extension captures DOM → Native messaging returns data → Python converts to markdown

**Key patterns:**
1. Accessibility API for window/tab control (no CDP)
2. Native messaging for Python↔Extension communication
3. Content script injected on-demand for DOM capture

## Module Boundaries

| Module | Owns | Never Import |
|--------|------|--------------|
| `src/orchestrator/` | Browser control via A11y | Selenium, Playwright, CDP |
| `src/utils/` | Pure conversion functions | Browser interaction |
| `extension/` | DOM access, messaging | Python modules |

## Key Files

| File | Purpose |
|------|---------|
| `src/orchestrator/controller.py` | Main `BrowserController` class |
| `src/orchestrator/accessibility.py` | PyObjC wrappers |
| `src/utils/dom_to_markdown.py` | DOM → Markdown conversion |
| `extension/manifest.json` | MV3 extension config |
| `extension/background.js` | Service worker, message routing |
| `extension/content.js` | DOM capture, runs in page context |

## Quirks

1. **PyObjC import order** (`src/orchestrator/accessibility.py:1-5`)
   - Must import `AppKit` before `ApplicationServices`
   - Wrong order causes cryptic errors

2. **Accessibility permissions** (System Preferences)
   - App must be granted Accessibility access
   - First run will prompt, subsequent runs won't

3. **Brave bundle ID** (`src/orchestrator/detector.py:34`)
   - Brave: `com.brave.Browser`, not `com.google.Chrome`

4. **MV3 service worker lifecycle** (`extension/background.js`)
   - Terminates after 30s idle
   - Use `chrome.storage` not variables for state

5. **Native messaging host** (`scripts/install_host.py`)
   - Must be installed separately
   - Path in manifest must be absolute

## Platform Gotchas

### macOS
- Accessibility permissions: System Preferences → Privacy → Accessibility
- First grant requires app restart

### Chrome MV3
- No persistent background pages
- `chrome.storage.local` for persistence
- Content scripts can't use `chrome.*` directly

## Testing

- **Framework:** pytest
- **Location:** `tests/`
- **Run:** `just test`
- **Count:** 28 tests

**Note:** Most tests mock the Accessibility API — real browser tests require manual setup.

## How to Work Here

1. Start Brave manually before running
2. Grant Accessibility permissions on first run
3. Extension must be installed in Brave (dev mode)
4. Test A11y code changes carefully — errors are often cryptic
5. Check Quirks section when something unexpected happens
```

---

## After Generation

Once you've created `docs/BOOTSTRAP_PROMPT.md`, inform the user:

> **BOOTSTRAP_PROMPT.md created** ([X] lines).
>
> This file provides deep architectural understanding:
> - [X] files in reading list
> - [X] module boundaries documented
> - [X] quirks with file:line references
> - [Infrastructure diagram if applicable]
>
> Next: `"Read .prompts/SESSION_GENERATOR.md and create docs/SESSION_HANDOFF.md"`
