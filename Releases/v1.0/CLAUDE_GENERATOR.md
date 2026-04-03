# CLAUDE_GENERATOR.md
<!-- Version: 1.0 -->

> **What this file does:** Generates a lean, rules-focused `CLAUDE.md` file for the project root. This is the file Claude Code reads automatically at session start.
>
> **How to run:** Prompt Claude Code with: `"Read .prompts/CLAUDE_GENERATOR.md and create CLAUDE.md"`
>
> **Pre-requisite:** Run PROJECT_SURVEY.md first. The survey output must be in context.
>
> **Output:** Creates `CLAUDE.md` in the project root (renames existing to `CLAUDE_old.md` if present).

---

## Instructions for Claude Code

You are about to generate `CLAUDE.md` for this project. This file will be read automatically by Claude Code at the start of every session, so it must be:

1. **Lean** — Under 200 lines (ideally 100-150)
2. **Scannable** — Tables, categorized sections, no walls of text
3. **Rules-focused** — What to do, what not to do, essential commands
4. **Non-duplicative** — Deep explanations go in BOOTSTRAP_PROMPT.md, not here

**Use the PROJECT_SURVEY_OUTPUT.md** (already in context) as your source of truth.

---

## File Handling

Before creating the new file:

1. Check if `CLAUDE.md` exists in the project root
2. If yes, rename it to `CLAUDE_old.md`
3. If `CLAUDE_old.md` already exists, rename to `CLAUDE_old2.md` (and so on)
4. Create the new `CLAUDE.md`

---

## Document Structure

Generate `CLAUDE.md` with the following sections. **Omit any section that doesn't apply** — empty sections waste tokens.

### Section 1: Project Title & One-Liner

```markdown
# [Project Name]

[One sentence describing what this project does — be specific, not generic]
```

**Good:** "A full-stack Astro app that extracts structured data from tax PDFs using local LLM inference."

**Bad:** "A web application for document processing."

---

### Section 2: Quick Reference (Commands)

List essential commands the developer will use frequently. Categorize if more than 10 commands.

**For <10 commands:**
```markdown
## Commands

```bash
just dev          # Start dev server
just test         # Run all tests
just db-up        # Start PostgreSQL container
just db-migrate   # Run database migrations
just lint         # Lint and format
```
```

**For 10+ commands, categorize:**
```markdown
## Commands

### Development
```bash
just dev          # Start dev server with hot reload
just dev-debug    # Start with debug logging
```

### Database
```bash
just db-up        # Start PostgreSQL container
just db-down      # Stop PostgreSQL container
just db-migrate   # Run pending migrations
just db-reset     # Drop and recreate database
```

### Testing
```bash
just test         # Run all tests
just test-watch   # Run tests in watch mode
just test-cov     # Run with coverage report
```

### Deployment
```bash
just build        # Production build
just deploy       # Deploy to production
```
```

**Source commands from:** Justfile, Makefile, package.json scripts, or pyproject.toml scripts.

---

### Section 3: Tech Stack

Table format, one row per layer. Include only what's actively used.

```markdown
## Tech Stack

| Layer | Technology |
|-------|------------|
| Runtime | Bun 1.x |
| Framework | Astro 5 (SSR) |
| Language | TypeScript (strict) |
| Database | PostgreSQL 16 |
| ORM | Drizzle |
| Auth | Better Auth |
| Testing | bun:test |
| LLM | Qwen 3 via LM Studio (remote) |
```

---

### Section 4: Rules

Split into three categories. Be specific and actionable.

```markdown
## Rules

### Boundaries — Never Cross These

1. **`src/db/`** owns all database access — never import `drizzle` elsewhere
2. **`src/llm/`** owns all LLM calls — never call the LLM API directly from routes
3. **`src/auth/`** owns authentication — never check sessions outside this module
4. Never commit `.env` files — use `.env.example` for templates
5. Never use `any` type — if you think you need it, you don't

### Patterns — Follow These

1. All API routes return `{ success: boolean, data?: T, error?: string }`
2. Use Zod schemas for all request/response validation
3. Errors are logged with Pino, never `console.log`
4. Database queries use prepared statements via Drizzle
5. All dates stored as UTC, displayed in user's timezone
6. File paths use `path.join()`, never string concatenation

### Gotchas — Things That Have Bitten Us

1. **Qwen 3 thinks by default** — append `/no_think` to prompts for simple extractions or you'll get chain-of-thought in the output
2. **First LLM request is slow** — JIT model loading takes 10-30s, subsequent requests <2s
3. **HTMX needs `hx-boost="false"`** on file upload forms or they'll break
4. **Drizzle migrations are append-only** — never edit existing migration files
5. **macOS Keychain prompts** — first `git push` per session may prompt for credentials
```

---

### Section 5: Machine Context (If Multi-Machine)

Only include if the project spans multiple machines.

```markdown
## Machine Context

| Machine | Role | Address |
|---------|------|---------|
| Mac Mini M4 | Dev machine, runs Astro app | localhost |
| RTX 3090 PC | LLM inference (LM Studio) | 192.168.1.56:1234 |
| Docker | PostgreSQL only | localhost:5432 |

**Network notes:**
- LLM API has no authentication — security is network-layer (LAN only)
- Database runs in Docker on dev machine, not remote
```

---

### Section 6: Conventions

Brief notes on project conventions.

```markdown
## Conventions

- **Default branch:** `master`
- **Runner:** `bun run` for scripts, `bunx` for one-off commands
- **Python (if any):** `uv run python3`
- **Commits:** Conventional commits (`feat:`, `fix:`, `docs:`, etc.)
- **PRs:** Not used — single developer, commit directly to master
```

---

### Section 7: Links to Further Docs

Reference other documentation for deeper context.

```markdown
## Further Reading

- **Deep onboarding:** `docs/BOOTSTRAP_PROMPT.md` — architecture, module boundaries, quirks
- **Current state:** `docs/SESSION_HANDOFF.md` — what's in progress, what's next
- **History:** `docs/EVOLUTION.md` — decisions made, features added
- **Architecture:** `docs/ARCHITECTURE.md` — detailed system design (if exists)
```

---

## Quality Checklist

Before presenting the generated CLAUDE.md, verify:

- [ ] **Under 200 lines** (ideally 100-150)
- [ ] **No duplicate content** — if it's explained in BOOTSTRAP, just reference it
- [ ] **Commands are accurate** — pulled from actual Justfile/Makefile/package.json
- [ ] **Rules are specific** — "never do X" with reason, not vague guidelines
- [ ] **Gotchas are real** — from survey's quirk discovery, not hypothetical
- [ ] **Tables are used** — for tech stack, machine context, anything with 3+ items
- [ ] **No empty sections** — omit sections that don't apply
- [ ] **One-liner is specific** — describes THIS project, not a generic category

---

## Examples

### Example 1: Minimal CLAUDE.md (~20 lines)

For a simple project with few rules:

```markdown
# url-shortener

A CLI tool that shortens URLs using the is.gd API.

## Commands

```bash
uv run python3 -m urlshort <url>    # Shorten a URL
uv run pytest                        # Run tests
```

## Conventions

- **Default branch:** `master`
- **Runner:** `uv run python3`

## Further Reading

- `docs/BOOTSTRAP_PROMPT.md` — architecture and patterns
```

---

### Example 2: Medium CLAUDE.md (~80 lines)

For a browser automation project:

```markdown
# browser-automation

Captures any web page's DOM as markdown using macOS Accessibility API and a Chrome MV3 extension — fully undetectable (no CDP, no Playwright).

## Commands

```bash
just run                    # Start the orchestrator
just extension-build        # Build the Chrome extension
just extension-install      # Install to Brave (dev mode)
just test                   # Run all tests
```

## Tech Stack

| Layer | Technology |
|-------|------------|
| Runtime | Python 3.12 |
| Browser Control | macOS Accessibility API (PyObjC) |
| Extension | Chrome MV3 (Manifest V3) |
| Testing | pytest |

## Rules

### Boundaries

1. **`src/orchestrator/`** owns browser automation — never import PyObjC elsewhere
2. **`extension/`** is isolated — communicates only via native messaging
3. Never use CDP or Playwright — defeats the "undetectable" goal

### Patterns

1. All browser commands go through `BrowserController` class
2. Extension responses are JSON with `{ success, data, error }` shape
3. Timeouts are explicit, never rely on defaults

### Gotchas

1. **PyObjC imports are picky** — must import `AppKit` before `ApplicationServices`
2. **Accessibility permissions** — app must be granted access in System Preferences
3. **MV3 service workers** — no persistent state, use chrome.storage
4. **Brave detection** — bundle ID is `com.brave.Browser`, not Chrome

## Conventions

- **Default branch:** `master`
- **Runner:** `uv run python3`
- **Browser:** Brave only (Chrome works but untested)

## Further Reading

- `docs/BOOTSTRAP_PROMPT.md` — architecture diagram, module boundaries
- `docs/SESSION_HANDOFF.md` — current implementation status
```

---

### Example 3: Comprehensive CLAUDE.md (~150 lines)

For a full-stack app with multiple services:

```markdown
# tax-pipeline

A full-stack Astro 5 SSR application that extracts structured data from PDF documents (Australian tax invoices, receipts, bank statements) using local LLM inference via LM Studio on an RTX 3090, with results stored in PostgreSQL.

## Commands

### Development
```bash
just dev              # Start Astro dev server
just dev-debug        # Start with verbose logging
```

### Database
```bash
just db-up            # Start PostgreSQL container
just db-down          # Stop PostgreSQL container
just db-migrate       # Run pending migrations
just db-studio        # Open Drizzle Studio
just db-reset         # Drop and recreate (destructive!)
```

### Testing
```bash
just test             # Run all tests
just test-watch       # Watch mode
just test-llm         # Test LLM connection only
```

### Utilities
```bash
just lint             # Lint and format
just typecheck        # TypeScript check
just build            # Production build
```

## Tech Stack

| Layer | Technology |
|-------|------------|
| Runtime | Bun 1.x |
| Framework | Astro 5 (SSR mode) |
| Language | TypeScript (strict) |
| Database | PostgreSQL 16 (Docker) |
| ORM | Drizzle + Drizzle Kit |
| Auth | Better Auth + Drizzle adapter |
| Validation | Zod |
| Logging | Pino |
| LLM Client | TanStack AI |
| PDF Parsing | pdfjs-dist |
| Interactivity | HTMX (CDN) |
| Testing | bun:test |

## Rules

### Boundaries

1. **`src/db/`** — owns all database access, exports only query functions
2. **`src/llm/`** — owns all LLM calls, never import TanStack AI elsewhere
3. **`src/auth/`** — owns authentication, never check sessions outside
4. **`src/pdf/`** — owns PDF parsing, never import pdfjs elsewhere

### Patterns

1. API responses: `{ success: boolean, data?: T, error?: string }`
2. All validation via Zod schemas in `src/schemas/`
3. Errors logged with Pino, never `console.log`
4. Dates stored UTC, displayed in `Australia/Melbourne`
5. File uploads to `uploads/` with UUID filenames

### Gotchas

1. **Qwen 3 thinks by default** — use `/no_think` suffix for extractions
2. **First LLM request: 10-30s** — JIT model loading, subsequent <2s
3. **HTMX file uploads** — need `hx-boost="false"` on forms
4. **Drizzle migrations** — append-only, never edit existing files
5. **pdfjs worker** — must copy worker to public/ on build

## Machine Context

| Machine | Role | Address |
|---------|------|---------|
| Mac Mini M4 | Dev + Astro app | localhost |
| RTX 3090 PC | LLM inference | 192.168.1.56:1234 |
| Docker | PostgreSQL | localhost:5432 |

**LLM Models:**

| Model | Use Case | Notes |
|-------|----------|-------|
| qwen3-8b | Text extraction | Fast, good for structured data |
| qwen2.5-vl-7b | Image/PDF with visuals | Slower, use only when needed |

## Conventions

- **Default branch:** `master`
- **Runner:** `bun run`, `bunx`
- **Commits:** Conventional (`feat:`, `fix:`, `docs:`)
- **No PRs:** Single developer, direct commits

## Further Reading

- `docs/BOOTSTRAP_PROMPT.md` — architecture, module boundaries, all quirks
- `docs/SESSION_HANDOFF.md` — current status, what's next
- `docs/EVOLUTION.md` — decisions and changes log
- `docs/ARCHITECTURE.md` — detailed system design
- `docs/LLM.md` — LLM-specific patterns and quirks
```

---

## After Generation

Once you've created `CLAUDE.md`, inform the user:

> **CLAUDE.md created** ([X] lines).
>
> This file will be read automatically by Claude Code at session start. It contains:
> - [X] commands in [Y] categories
> - [X] boundary rules, [X] patterns, [X] gotchas
> - [Machine context if applicable]
>
> Next: `"Read .prompts/BOOTSTRAP_GENERATOR.md and create docs/BOOTSTRAP_PROMPT.md"`
