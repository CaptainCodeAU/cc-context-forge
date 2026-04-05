# BOOTSTRAP_GENERATOR.md

<!-- Version: 2.2 -->

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

1. **Comprehensive** — Sized to project complexity (see Content Allocation Guide below)
2. **Pedagogical** — Ordered reading list, progressive understanding
3. **Specific** — Real file paths, real exports, real quirks with file:line references
4. **Non-duplicative** — Rules and full command dumps live in CLAUDE.md, not here. A quick command reference (8-12 most-used commands) IS appropriate.
5. **API-reference quality** — Key files list exports by name. A reader should know what a module provides without opening it.

> **The cardinal rule:** If removing a line from BOOTSTRAP_PROMPT.md would force the next session to open a source file to understand the architecture, keep the line. BOOTSTRAP exists to prevent source-file reads during onboarding.

**Use the PROJECT_SURVEY_OUTPUT.md** (already in context) as your source of truth.

---

## Content Allocation Guide

Before writing, assess the project's complexity from the survey output.

### Step 1 — Complexity Assessment

Score the project on three axes:

| Axis                         | Simple         | Medium             | Complex                      |
| ---------------------------- | -------------- | ------------------ | ---------------------------- |
| Modules                      | 1-3            | 4-7                | 8+                           |
| Pipeline stages / data flows | 1              | 2-3                | 4+                           |
| Infrastructure               | Single machine | Docker or 1 remote | Multi-machine, multi-service |

The **highest** axis determines the tier.

### Step 2 — Line Budget

| Tier    | Target Range  | Hard Maximum |
| ------- | ------------- | ------------ |
| Simple  | 80-130 lines  | 150          |
| Medium  | 130-175 lines | 200          |
| Complex | 175-250 lines | 300          |

### Step 3 — Space Allocation (approximate %)

| Section                | Simple | Medium | Complex |
| ---------------------- | ------ | ------ | ------- |
| Header + Reading List  | 15%    | 12%    | 10%     |
| What This Is           | 5%     | 4%     | 3%      |
| Infrastructure Diagram | skip   | 8%     | 8%      |
| Architecture           | 15%    | 18%    | 20%     |
| Module Boundaries      | 10%    | 10%    | 10%     |
| Key Files              | 15%    | 15%    | 15%     |
| Quirks & Gotchas       | 15%    | 15%    | 18%     |
| Testing                | 5%     | 5%     | 4%      |
| Commands (quick ref)   | 5%     | 5%     | 4%      |
| How to Work Here       | 5%     | 5%     | 4%      |

**Note:** Architecture and Quirks get the largest allocations. These are the sections that save the most time by preventing source-file reads. Commands and rules are already covered in CLAUDE.md.

---

## Cross-Reference Guide — What Goes Where

When deciding whether content belongs in BOOTSTRAP or another file:

| Content Type                  | CLAUDE.md         | BOOTSTRAP                                   | SESSION | EVOLUTION |
| ----------------------------- | ----------------- | ------------------------------------------- | ------- | --------- |
| Full command reference        | YES               | Quick ref (8-12 cmds)                       | —       | —         |
| Module boundaries (ownership) | YES (1-line each) | Detailed table with exports                 | —       | —         |
| Gotchas with fixes            | YES (1-line each) | Detailed with file:line, root cause, commit | —       | —         |
| Architecture explanation      | —                 | YES (primary home)                          | —       | —         |
| Ordered reading list          | —                 | YES                                         | —       | —         |
| Key file exports              | —                 | YES (API reference)                         | —       | —         |
| Shared type definitions       | —                 | YES (in reading list)                       | —       | —         |
| Subsystem isolation details   | —                 | YES (in architecture)                       | —       | —         |
| Complete quirks catalogue     | —                 | YES (ALL bugs)                              | —       | —         |
| Data flow diagrams            | —                 | YES                                         | —       | —         |
| Infrastructure diagram        | —                 | YES                                         | —       | —         |
| Env var reference table       | YES               | —                                           | —       | —         |
| Model IDs / service details   | YES               | —                                           | —       | —         |
| Current state / WIP           | —                 | —                                           | YES     | —         |
| Decision history / "why"      | —                 | —                                           | —       | YES       |

**The litmus test:** If it helps understand _how the system works_, it belongs in BOOTSTRAP. If it's needed to _execute a command correctly_, it belongs in CLAUDE.md. If it's _current session state_, it belongs in SESSION. If it explains _why a decision was made_, it belongs in EVOLUTION.

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
2. `src/config/types.ts` — Shared types used across all modules
3. `src/db/schema.ts` — Database schema, the source of truth for data
4. `src/config/config.ts` — Dynamic config loader
5. `src/lib/db.ts` — Database client, types, helpers
6. `src/llm/client.ts` — LLM integration patterns
7. `src/signals/types.ts` — Signal type definitions
8. `.env.example` — Required environment variables

### Optional (for deep dives)

- `src/pdf/parser.ts` — PDF extraction logic (read if working on PDF features)
- `src/auth/config.ts` — Auth configuration (read if working on auth)
- `src/signals/patterns/currency.ts` — Currency extraction patterns (read if working on signals)
```

**Guidelines for reading list:**

- 6-10 mandatory files, 4-8 optional files
- Order from general to specific
- Include config files that reveal architecture
- **Always include shared type definition files** (e.g., `types.ts`, `interfaces.ts`) — these define the vocabulary every module uses
- **Always include schema files** (database schema, API schemas) — these are the source of truth for data structures
- Optional files grouped by feature area / subsystem
- For projects with a signal/extraction/pattern subsystem, include the types file and orchestrator in mandatory, individual pattern files in optional

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
│ Mac Mini M4 (Dev) │
│ ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐ │
│ │ App │ │ Docker │ │ Claude Code │ │
│ │ :4321 │ │ Postgres │ │ │ │
│ └──────┬──────┘ │ :5432 │ └─────────────────────┘ │
│ │ └─────────────┘ │
└─────────┼───────────────────────────────────────────────────┘
│ HTTP API
▼
┌─────────────────────────────────────────────────────────────┐
│ GPU Server (LLM) │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ LM Studio :1234 │ │
│ │ ┌─────────────┐ ┌─────────────┐ (one at a time) │ │
│ │ │ model-a │ │ model-b │ │ │
│ │ └─────────────┘ └─────────────┘ │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘

```

**Network notes:**
- [Connection details, auth, latency notes]
```

---

### Section 5: Architecture in Brief

One-line data flow, then key architectural patterns, then subsystem isolation descriptions.

```markdown
## Architecture

**Data flow:** [End-to-end pipeline in one line, using arrows]

**Key patterns:**

1. **[Pattern name]** — [Specific mechanism, not a vague principle]
2. **[Pattern name]** — [Specific mechanism]
3. ...

**Subsystem isolation:**

- **[Subsystem A]** — [What it writes to, what it never touches, how it's reversed]
- **[Subsystem B]** — [Same pattern]
- **[Subsystem C]** — [Same pattern]
```

**Guidelines for architecture:**

- The data flow line should be understandable without reading source
- Key patterns: 3-7 bullets. Each must name a SPECIFIC mechanism, not a vague principle
    - **Bad:** "Module isolation — each domain is self-contained" (too vague)
    - **Good:** "Three-layer reversibility — each pipeline stage is independently re-runnable. Download is idempotent (state.json), ingest has run tracking + rollback, filter creates new rows, classify supports --resume"
- Subsystem isolation: 2-5 entries. Each must name the tables/data it owns and what it does NOT touch
- If the project has a multi-stage pipeline, describe how each stage relates to the previous one
- If the project uses an LLM, describe which stages use it and which are deterministic

---

### Section 6: Module Boundaries Table

Clear ownership table. This is the "never cross these lines" reference.

```markdown
## Module Boundaries

| Module      | Owns                      | Exports                             | Never Import Here       |
| ----------- | ------------------------- | ----------------------------------- | ----------------------- |
| `src/db/`   | All database access       | Query functions, types              | Raw SQL, other ORMs     |
| `src/llm/`  | All LLM communication     | `extractData()`, `generateSchema()` | Direct API calls        |
| `src/auth/` | Authentication & sessions | `getSession()`, `requireAuth()`     | Session storage details |

**Rule:** Import from a module's public API (index.ts or explicit exports), never reach into internal files.
```

---

### Section 7: Key Files and What They Do

10-25 most important files with their purpose AND key exports. This section should have **API-reference quality** — a reader should know what a module provides without opening it.

```markdown
## Key Files

| File                  | Purpose            | Size      | Key Exports                                                        |
| --------------------- | ------------------ | --------- | ------------------------------------------------------------------ |
| `src/db/schema.ts`    | Database schema    | 649 lines | All table definitions, 2 views, 2 triggers                         |
| `src/db/queries.ts`   | Database queries   | 12KB      | `getDocuments()`, `saveExtraction()`, `notIgnoredEmail()`          |
| `src/llm/client.ts`   | LLM client wrapper | 15KB      | `chatJSON()`, `chatText()`, `ensureServerReady()`, `checkHealth()` |
| `src/config/types.ts` | Shared types       | 2KB       | `MailboxConfig`, `UserConfig`, `Provider`                          |
```

**Guidelines for key files:**

- **Key Exports column is mandatory.** List 2-5 most important exports by name. These are the functions/classes/types other code imports.
- Include the Size column (lines or KB) to signal complexity — a 64KB file is architecturally significant
- **List every file in subsystems individually** — do not collapse to "signals/ — 7 pattern files". Each file has different exports that callers need to know about.
- **Include shared type files** (`types.ts`, `interfaces.ts`) — these define the vocabulary
- For large subsystems (5+ files), use sub-groupings with a bold label:
    ```
    **Signal extraction:**
    | `src/signals/extractor.ts` | Orchestrator | — | `extractSignalsForEmail()`, `extractSignalsBatch()` |
    | `src/signals/patterns/currency.ts` | Currency/GST | — | `extractCurrency()`, `extractGST()` |
    ...
    ```
- Sort by importance within each group, not alphabetically
- **Include ALL library modules** — every file in the project's library directory (`src/lib/`, `src/utils/`, etc.) that exports a public API must appear in Key Files, even if small. A 41-line rate limiter is still an API surface that callers need to know about. The survey output lists all modules — cross-check against it.
- If a file is the sole importer of a package, note it: "(sole postgres importer)"

---

### Section 8: Quirks That Have Caused Bugs

**Completeness requirement:** List ALL production bugs that have been discovered and fixed, not just a representative sample. Every bug that cost debugging time is worth documenting — the cost of an extra line is near zero, the cost of re-discovering a bug is hours.

Numbered list with file:line references, root cause, and fix details. Include commit hashes for fixes when available in git history.

```markdown
## Quirks & Gotchas

These have caused real bugs. Learn from our pain:

1. **[Bug title]** (`src/file.ts:line`)
    - [What happens / symptoms]
    - Root cause: [Why it happens]
    - Fix: [What was done]. Commit: `abc1234`

2. **[Bug title]** (`src/other.ts:line`)
    - [What happens]
    - Fix: [What was done]

[...continue for ALL known bugs...]
```

**Guidelines for quirks:**

- **List every bug** — if the survey output mentions it, include it. If git history has a fix commit, include it.
- Include root cause when known (not just the symptom)
- Include commit hash for the fix when available
- **Categorize if 10+ quirks:** Group by subsystem (e.g., LLM, Database, IMAP, Pipeline, Platform)
- For complex projects, expect 12-20+ quirks. If you have fewer than 10 for a complex project, you are likely missing some — re-examine the survey output and git history.
- Each quirk should name the specific file and line where the fix lives
- **Do NOT skip pipeline/ingestion war stories** — bugs in data processing stages (duplicate hashes, timeouts, flag parsing, batch failures) are among the most valuable to document because they are the hardest to re-diagnose
- **Include defensive patterns that silently prevent bugs** — sanitisation functions, guard clauses, and encoding workarounds are quirks too. If code exists specifically because raw/unguarded usage causes failures (e.g., `sanitizeJsonString()` strips control chars from LLM output because `JSON.parse()` fails without it, or `sanitizeFolder()` replaces `/` with `__` because folder paths can't be directory names), document them. The test is: "Would someone unfamiliar with this codebase naively skip this step and hit a bug?" If yes, it's a quirk.

---

### Section 9: Platform/Framework-Specific Gotchas

Separate section for platform-level issues (not project-specific bugs).

```markdown
## Platform Gotchas

### macOS

- Accessibility permissions required for browser automation
- First `git push` per session may trigger Keychain prompt
- Docker Desktop must be running for PostgreSQL

### Bun

- Not all Node.js APIs available
- `process.env` works, `import.meta.env` does not
```

---

### Section 10: Testing Conventions

How tests are organized and run.

```markdown
## Testing

- **Framework:** bun:test
- **Location:** `tests/` mirrors `src/` structure
- **Run all:** `just test` or `bun test`

**Patterns:**

- Unit tests for pure functions
- Integration tests use real services, skip gracefully when unavailable
- Factory helpers: `makeEmail()`, `makeRule()`

**Current count:** ~512 tests across 24 files
```

---

### Section 11: Essential Commands

Brief quick-reference — full list is in CLAUDE.md. Include 8-12 most commonly used commands.

````markdown
## Commands (Quick Reference)

```bash
just dev          # Start dev server
just test         # Run tests
just db-up        # Start database
just db-migrate   # Run migrations
just lint         # Lint & format
just build        # Production build
```
````

See `CLAUDE.md` for the complete command reference.

````

---

### Section 12: How to Work in This Project

Numbered rules for how to approach work. These are workflow guidelines, not rules (rules live in CLAUDE.md).

```markdown
## How to Work Here

1. **Start by reading** — CLAUDE.md (auto), then paste this file
2. **Check existing patterns** — before creating something new, find 2-3 similar examples in the codebase
3. **Stay in your module** — if you need something from another module, use its exports
4. **Test locally first** — especially LLM features, don't assume they work
5. **Ask about quirks** — if something behaves unexpectedly, check the Quirks section first
````

---

## Anti-Patterns — What NOT to Include

These patterns cause BOOTSTRAP_PROMPT.md to drift toward duplicating CLAUDE.md or bloating:

1. **Full command dump** — CLAUDE.md owns the complete command reference. BOOTSTRAP has a quick-ref of 8-12 commands max.
2. **Env var reference table** — CLAUDE.md owns this. BOOTSTRAP mentions `.env.example` in the reading list.
3. **Rule statements without architecture context** — "No `any` types" is a rule (CLAUDE.md). "Classification writes to its own tables and never touches ingest data" is architecture (BOOTSTRAP).
4. **Vague architecture bullets** — "Module isolation" says nothing. "Single-owner imports — each external package imported in exactly one module" says everything.
5. **Key files without named exports** — A file entry that says "Database queries" or "System/user prompt builders" instead of "`getDocuments()`, `saveExtraction()`" or "`buildSystemPrompt()`, `buildBatchPrompt()`" wastes the entry. Every export must be a real function, class, or type name — no prose descriptions of what the exports do.
6. **Collapsed subsystems** — "signals/ — pattern extraction" hides 7 files with distinct exports. List them individually.
7. **Representative quirk samples** — "Here are the top 8 bugs" when there are 16. List all of them.
8. **Generic "How to Work" rules** — "Write clean code" adds nothing. "Respect the ignore layer — all email queries must use `notIgnoredEmail(sql)`" is specific and actionable.

---

## Quality Checklist

Before presenting the generated BOOTSTRAP_PROMPT.md, verify:

**Sizing**

- [ ] Line count is within the target range for this project's complexity tier?
- [ ] Architecture section gets 15-20% of the line budget?
- [ ] Would removing any line require a developer to open a source file?

**Key Files Quality**

- [ ] Every key file entry lists 2-5 exports by name?
- [ ] Shared type files (`types.ts`, `interfaces.ts`) are in the reading list?
- [ ] Subsystem files are listed individually, not collapsed?
- [ ] Every file in the library directory (`src/lib/`, `src/utils/`, etc.) with public exports is listed?
- [ ] Size column populated for files over 10KB?

**Architecture Quality**

- [ ] Architecture section has subsystem isolation descriptions (not just pattern bullets)?
- [ ] Each subsystem names its tables/data and what it does NOT touch?
- [ ] Multi-stage pipelines describe stage relationships?

**Quirks Completeness**

- [ ] ALL known bugs from survey output are listed (not just representative ones)?
- [ ] Quirk count matches or exceeds what's in the survey output?
- [ ] Each quirk has file:line reference where applicable?
- [ ] Commit hashes included for fixes where available?
- [ ] Pipeline/ingestion war stories are NOT omitted?

**Non-Duplication**

- [ ] No full command dump (8-12 quick-ref max)?
- [ ] No env var reference table (that's in CLAUDE.md)?
- [ ] No rule statements without architecture context?
- [ ] Reading list is ordered and curated (not a file dump)?

**Accuracy**

- [ ] Key files actually exist — verify paths are correct?
- [ ] Module boundaries table is complete?
- [ ] Infrastructure diagram is accurate (if applicable)?
- [ ] Testing section reflects reality (framework, location, count)?

---

## Examples

### Example 1: Simple Project — Python CLI (~100 lines)

```markdown
# Bootstrap Prompt — url-shortener

> **How to use:** Copy and paste this entire file into Claude Code at the start of a new session.
>
> **Last updated:** 2026-04-01

## Reading List

### Mandatory

1. `CLAUDE.md` — Rules and commands
2. `src/types.py` — Shared types (`URLResult`, `Config`)
3. `src/cli.py` — Entry point, argument parsing
4. `src/shortener.py` — Core shortening logic
5. `src/api_client.py` — is.gd API integration
6. `pyproject.toml` — Dependencies and config

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

1. **Click for CLI parsing** — all argument handling via Click decorators, not argparse
2. **httpx for HTTP** — async-capable, but used synchronously for simplicity
3. **pyperclip for clipboard** — optional dependency, fails gracefully if absent

## Module Boundaries

| Module              | Owns                          | Exports                               |
| ------------------- | ----------------------------- | ------------------------------------- |
| `src/cli.py`        | Argument parsing, output      | `main()`                              |
| `src/shortener.py`  | URL validation, orchestration | `shorten_url()`, `validate_url()`     |
| `src/api_client.py` | is.gd API communication       | `IsGdClient`, `shorten()`, `MAX_RATE` |

## Key Files

| File                | Purpose         | Key Exports                           |
| ------------------- | --------------- | ------------------------------------- |
| `src/cli.py`        | CLI entry point | `main()`, Click commands              |
| `src/shortener.py`  | Core logic      | `shorten_url()`, `validate_url()`     |
| `src/api_client.py` | API client      | `IsGdClient`, `shorten()`, `MAX_RATE` |
| `src/types.py`      | Shared types    | `URLResult`, `Config`, `APIError`     |
| `pyproject.toml`    | Project config  | Dependencies, entry points            |

## Quirks

1. **is.gd rate limits** (`src/api_client.py:15`) — max 10 requests/minute from same IP. Fix: token-bucket rate limiter in `IsGdClient`.
2. **pyperclip fails silently** (`src/cli.py:34`) — clipboard copy is best-effort. Root cause: no clipboard daemon on headless Linux. Fix: try/except with warning.

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

### Example 2: Medium Project — Browser Automation (~150 lines)

```markdown
# Bootstrap Prompt — browser-automation

> **How to use:** Copy and paste this entire file into Claude Code at the start of a new session.
>
> **Last updated:** 2026-04-01

## Reading List

### Mandatory

1. `CLAUDE.md` — Rules and commands
2. `src/types.py` — Shared types (`CaptureResult`, `BrowserState`, `WindowInfo`)
3. `src/orchestrator/controller.py` — Main browser controller
4. `src/orchestrator/accessibility.py` — macOS Accessibility API wrapper
5. `extension/manifest.json` — MV3 manifest
6. `extension/background.js` — Extension service worker
7. `extension/content.js` — DOM capture script

### Optional

- `src/utils/dom_to_markdown.py` — DOM conversion logic (if working on output formatting)
- `docs/ARCHITECTURE.md` — Detailed flow diagrams (if making major changes)

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
│ macOS Dev Machine │
│ ┌─────────────┐ ┌─────────────────┐ │
│ │ Python │ │ Brave │ │
│ │ Orchestrator│◄────►│ + Extension │ │
│ │ │ A11y │ │ │
│ │ │ API │ │ │
│ └─────────────┘ └─────────────────┘ │
└─────────────────────────────────────────────┘

````

Communication: Python → Accessibility API → Brave window → Extension (native messaging)

## Architecture

**Data flow:** Python requests page → A11y API activates Brave → Extension captures DOM → Native messaging returns data → Python converts to markdown

**Key patterns:**
1. **Accessibility API for window/tab control** — no CDP, no Playwright. Uses `NSAccessibility` protocol to find browser windows, read tab titles, and send click/keystroke events. Truly undetectable.
2. **Native messaging for Python<->Extension communication** — JSON messages over stdio, max 1MB per message. Host manifest installed at `~/Library/Application Support/Google/Chrome/NativeMessagingHosts/`.
3. **Content script injected on-demand** — not persistent. Injected per capture request via `chrome.scripting.executeScript()`, captures DOM, returns via message port.

**Subsystem isolation:**
- **Python orchestrator** — owns all A11y API interaction and file output. Never calls browser APIs directly. Can be tested independently with mock A11y responses.
- **Extension** — owns all DOM access. Communicates only via native messaging. Cannot import Python modules. Has its own test harness via Chrome extension test framework.

## Module Boundaries

| Module | Owns | Exports | Never Import Here |
|--------|------|---------|-------------------|
| `src/orchestrator/` | Browser control via A11y | `BrowserController`, `navigate()`, `capture()` | Selenium, Playwright, CDP |
| `src/utils/` | Pure conversion functions | `dom_to_markdown()`, `clean_html()` | Browser interaction |
| `extension/` | DOM access, messaging | `captureDOM()`, `getPageText()` | Python modules |

## Key Files

| File | Purpose | Key Exports |
|------|---------|-------------|
| `src/orchestrator/controller.py` | Main browser control | `BrowserController`, `navigate()`, `capture()`, `close()` |
| `src/orchestrator/accessibility.py` | PyObjC wrappers | `get_windows()`, `focus_window()`, `click_element()`, `send_keys()` |
| `src/utils/dom_to_markdown.py` | DOM conversion | `dom_to_markdown()`, `clean_html()`, `extract_links()` |
| `src/types.py` | Shared types | `CaptureResult`, `BrowserState`, `WindowInfo` |
| `extension/manifest.json` | MV3 config | Permissions, content script declarations |
| `extension/background.js` | Service worker | Message routing, native messaging host connection |
| `extension/content.js` | DOM capture | `captureDOM()`, `getPageText()`, `getSelectedText()` |

## Quirks

1. **PyObjC import order** (`src/orchestrator/accessibility.py:1-5`)
   - Must import `AppKit` before `ApplicationServices`
   - Root cause: PyObjC initializes ObjC runtime on first import; wrong order leaves runtime in bad state
   - Fix: enforced import order. Commit: `a1b2c3d`

2. **Accessibility permissions** (System Preferences)
   - App must be granted Accessibility access before first run
   - Root cause: macOS sandboxing. No programmatic way to request.
   - Fix: startup check with user-friendly error message

3. **Brave bundle ID** (`src/orchestrator/detector.py:34`)
   - Brave: `com.brave.Browser`, not `com.google.Chrome`
   - Fix: detection code checks both bundle IDs

4. **MV3 service worker lifecycle** (`extension/background.js`)
   - Terminates after 30s idle — any in-memory state is lost
   - Fix: all state persisted to `chrome.storage.local`

5. **Native messaging host path** (`scripts/install_host.py`)
   - Must be absolute path in the manifest JSON
   - Root cause: Chrome resolves relative paths from its own install dir, not the project
   - Fix: `install_host.py` writes absolute path dynamically. Commit: `d4e5f6a`

## Platform Gotchas

### macOS
- Accessibility permissions: System Preferences → Privacy → Accessibility
- First grant requires app restart

### Chrome MV3
- No persistent background pages — use service workers
- `chrome.storage.local` for persistence, not variables
- Content scripts can't use `chrome.*` APIs directly — must message background

## Testing

- **Framework:** pytest
- **Location:** `tests/`
- **Run:** `just test`
- **Count:** 28 tests

**Note:** Most tests mock the Accessibility API — real browser tests require manual Brave setup.

## Commands (Quick Reference)

```bash
just test          # Run all tests
just install-host  # Install native messaging host
just dev           # Run with debug logging
````

See `CLAUDE.md` for the complete command reference.

## How to Work Here

1. Start Brave manually before running
2. Grant Accessibility permissions on first run
3. Extension must be installed in Brave (dev mode)
4. Test A11y code changes carefully — errors are often cryptic
5. Check Quirks section when something unexpected happens

````

---

### Example 3: Complex Project — Data Pipeline (~220 lines)

```markdown
# Bootstrap Prompt — invoice-pipeline

> **How to use:** Copy and paste this entire file into Claude Code at the start of a new session, after Claude has read CLAUDE.md. This gives Claude deep understanding of the project architecture.
>
> **Last updated:** 2026-04-05

## Reading List

Read these files in order to understand the project:

### Mandatory (read all)

1. `CLAUDE.md` — Project rules, commands, conventions (Claude reads automatically)
2. `src/config/types.ts` — Shared types: `MailboxConfig`, `UserConfig`, `Provider`, `PipelineStage`
3. `src/db/schema.sql` — 600+ line schema: 13 tables, 2 views, triggers, seed data
4. `src/config/config.ts` — Dynamic config loader (reads .env, builds config hierarchy)
5. `src/lib/db.ts` — DB singleton, row types, ignore-layer helpers (sole postgres importer)
6. `src/lib/llm.ts` — LLM client with fallback chain, health checks, WOL auto-wake (sole openai importer)
7. `src/lib/json-parse.ts` — Robust JSON parsing for LLM output (fence stripping, sanitisation)
8. `src/signals/types.ts` — Signal type definitions (24 types, extraction config)
9. `src/signals/extractor.ts` — Signal extraction orchestrator
10. `.env.example` — 170-line env var reference with comments

### Optional (for deep dives)

- `src/scripts/ingest-records.ts` — Most complex script (60KB+): batching, dedup, SIGINT. Read if working on ingestion.
- `src/lib/classifier.ts` — LLM classification with retry/resume/rollback. Read if working on classification.
- `src/lib/filter-engine.ts` — Rule evaluation with 10+ operators. Read if working on filtering.
- `src/lib/rule-proposer.ts` — Pattern mining from uncertain records. Read if working on rules.
- `src/signals/patterns/` — 7 regex pattern extractors (currency, dates, references, business IDs, contact info)
- `src/signals/normalizers/` — 4 normalizers (currency, dates, business IDs, index)

## What This Project Is

invoice-pipeline is a 5-stage CLI pipeline that extracts financial data from 150K+ source records across multiple input accounts, using a local LLM for classification and PostgreSQL for storage. Built for personal use with a short deadline.

**Key characteristics:**
- Runs entirely local — local LLM on LAN, sensitive data never leaves the network
- Multi-machine: dev on Mac, LLM inference on separate GPU server via LAN
- 5-stage pipeline: download → ingest → filter → classify → signal extraction
- Single developer, direct commits to default branch

## Infrastructure

````

+-------------------------------------------------------------------+
| Mac (Dev Machine) |
| +---------------+ +---------------------+ +-----------------+ |
| | Claude Code | | Docker | | External Drives | |
| | (this session)| | PostgreSQL :10045 | | raw file storage| |
| +---------------+ +---------------------+ +-----------------+ |
| | |
+---------+---------------------------------------------------------+
| HTTP API (OpenAI-compatible, no auth — LAN isolation)
v
+-------------------------------------------------------------------+
| GPU Server (RTX 3090) |
| 192.168.1.56:1234 |
| +-------------------------------------------------------------+ |
| | LM Studio | |
| | +------------------+ +------------------+ (one at a time) | |
| | | model-27b | | model-9b | | |
| | | (primary) | | (fallback) | | |
| | +------------------+ +------------------+ | |
| +-------------------------------------------------------------+ |
+-------------------------------------------------------------------+

````

**Network notes:**
- No auth on LLM API — security via LAN isolation only
- First request per model: 10-30s (JIT loading), subsequent <2s
- One model loaded at a time — switching models auto-evicts current
- WOL auto-wake: `ensureServerReady()` handles ping → WOL → boot → service wait

## Architecture

**Data flow:** Source download (.raw to disk) → ingest (parse + store in PostgreSQL) → filter (rule-based include/exclude) → classify (LLM for uncertain records) → extract signals (regex patterns, no LLM)

**Key patterns:**

1. **Three-layer reversibility** — each pipeline stage is independently re-runnable. Download is idempotent (state.json), ingest has run tracking + rollback, filter creates new result rows per run, classify supports --resume. No stage mutates upstream data.
2. **First-match-wins rule evaluation** — rules sorted by priority (desc), first match determines result. Eliminates multi-match ambiguity. Builtin rules at priority 0, user rules at 50-100.
3. **Zero-LLM signal extraction** — 24 signal types extracted via regex + normalizers. No LLM needed for deterministic patterns (amounts, dates, business IDs, references). Keeps extraction fast and free.
4. **Single-owner imports** — each external package imported in exactly one module (postgres → db.ts, openai → llm.ts, imapflow → client.ts). Centralises error handling, prevents version conflicts.
5. **Three-level LLM fallback** — primary model retry → primary with backoff → fallback model from scratch. Handles JIT loading, server sleep, model OOM.

**Subsystem isolation:**

- **Ingestion** — writes to `records`, `attachments`, `ingest_runs`. Never touches filter or classification data. Each run gets a UUID; reversible via `rollback` command.
- **Filtering** — writes to `filter_runs` and `record_filter_results`. Never modifies ingested records. Re-running creates new result rows (additive). Uses a VIEW (`record_current_filter`) for latest result per record.
- **Classification** — writes to `llm_classification_runs`, `llm_classification_results`, `llm_batch_log`. Never touches ingest or filter data. Supports `--resume` for interrupted runs. Fully reversible via rollback/reset.
- **Signal extraction** — writes to `record_signals` as JSONB with denormalized primaries. Re-running re-extracts (idempotent via UNIQUE + ON CONFLICT). Zero LLM dependency.

## Module Boundaries

| Module | Owns | Key Exports |
|--------|------|-------------|
| `src/lib/db.ts` | `postgres` package, all DB types | `getDb()`, `closeDb()`, row interfaces, `computeDedupHash()`, `notIgnoredRecord()` |
| `src/lib/llm.ts` | `openai` package, LLM client | `chatJSON()`, `chatText()`, `ensureServerReady()`, `checkHealth()` |
| `src/lib/json-parse.ts` | JSON parsing for LLM output | `parseLLMJson()`, `parseLLMJsonLoose()`, `stripJsonFences()` |
| `src/lib/client.ts` | `imapflow` package | `SourceClient`, `sanitizeFolder()`, `shouldDownloadFolder()` |
| `src/lib/sanitise.ts` | All filename/path sanitisation | `sanitiseFilename()`, `sanitiseMessageId()`, `attachmentPath()` |
| `src/config/config.ts` | All env var reading | `loadConfig()`, `loadSource()` |
| `src/lib/filter-engine.ts` | Rule evaluation + execution | `evaluateCondition()`, `runFilter()`, `extractSenderDomain()` |
| `src/lib/classifier.ts` | LLM classification | `runClassification()`, `rollbackRun()` |
| `src/signals/extractor.ts` | Signal extraction pipeline | Orchestrates patterns → labeler → aggregator → counterparty |

## Key Files

| File | Purpose | Size | Key Exports |
|------|---------|------|-------------|
| `src/scripts/ingest-records.ts` | Bulk ingestion — batching, dedup, SIGINT | 64KB | CLI entry point |
| `src/scripts/manage-rules.ts` | Rule management — add, edit, propose, import/export | 35KB | CLI entry point |
| `src/scripts/download-source.ts` | Source download — resume, folder selection, state | 29KB | CLI entry point |
| `src/lib/classifier.ts` | LLM classification — batch, retry, resume, rollback | 26KB | `runClassification()`, `rollbackRun()`, `getStatus()` |
| `src/lib/rule-proposer.ts` | Pattern mining from uncertain records | 19KB | `mineUncertainPatterns()`, `parseConditionString()`, `buildExportPayload()` |
| `src/lib/filter-engine.ts` | Rule-based filter — 10+ operators | 18KB | `evaluateCondition()`, `runFilter()`, `loadRules()`, `extractSenderDomain()` |
| `src/lib/llm.ts` | LLM client — fallback chain, WOL, health | 15KB | `chatJSON()`, `chatText()`, `ensureServerReady()`, `checkHealth()` |
| `src/db/schema.sql` | Complete schema — 13 tables, 2 views, triggers | 649 lines | Table definitions, seed data |
| `src/lib/db.ts` | DB singleton, types, ignore layer | 11KB | `getDb()`, `closeDb()`, 15+ row interfaces, `notIgnoredRecord()` |
| `src/config/types.ts` | Shared types | 3KB | `SourceConfig`, `UserConfig`, `ProjectConfig`, `Provider` |
| `src/config/config.ts` | Config loader | 5KB | `loadConfig()`, `loadSource()` |

**Signal extraction:**
| File | Purpose | Key Exports |
|------|---------|-------------|
| `src/signals/extractor.ts` | Orchestrator | `extractSignalsForRecord()`, `extractSignalsBatch()`, `saveSignals()` |
| `src/signals/types.ts` | Type definitions | `SignalType` (24 types), `ExtractedSignal`, `RecordSignals` |
| `src/signals/patterns/currency.ts` | Currency/GST | `extractCurrency()`, `extractGST()` |
| `src/signals/patterns/dates.ts` | Date extraction | `extractDates()` (DD/MM/YYYY default) |
| `src/signals/patterns/references.ts` | Invoice/receipt/order refs | `extractReferences()` |
| `src/signals/patterns/business.ts` | ABN/ACN/BSB/BPAY | `extractBusinessNumbers()` |
| `src/signals/patterns/contact.ts` | Phone/email/URL | `extractContacts()` |
| `src/signals/patterns/helpers.ts` | Shared utilities | `runPatterns()`, `extractContext()`, `buildSignal()` |
| `src/signals/normalizers/currency.ts` | Currency normalisation | `normalizeCurrency()` |
| `src/signals/normalizers/dates.ts` | Date normalisation | `normalizeDate()` |
| `src/signals/normalizers/abn.ts` | ABN validation | `normalizeABN()`, `isValidABN()` |
| `src/signals/labeler.ts` | Context-aware labels | `detectLabels()` (reclassifies generic types) |
| `src/signals/aggregator.ts` | Primary value selection | `selectPrimaries()` (priority ordering) |
| `src/signals/counterparty.ts` | Counterparty matching | `lookupCounterparty()`, `clearCache()` |

## Quirks & Gotchas

### LLM / Inference Server

1. **Model returns empty `content`** (`src/lib/llm.ts:102`) — Thinking mode makes `content` null; actual response is in `reasoning_content`. Root cause: LM Studio bug #1602. Fix: fallback to `reasoning_content` automatically.

2. **Must disable thinking for JSON** (`src/lib/llm.ts:89`) — Always send `chat_template_kwargs: { enable_thinking: false }` or the model wraps JSON output in `<think>` tags. Fix: hardcoded in `callLLM()`.

3. **Max tokens must be 16000** — Reasoning token overhead means lower values truncate output silently. Fix: hardcoded default in `chatJSON()`.

4. **Three-level fallback** (`src/lib/llm.ts:229-283`) — Primary retry → primary with backoff → fallback model from scratch. Handles JIT loading delays and model OOM.

5. **Server often powered down** — GPU machine is frequently off. `ensureServerReady()` handles ping → WOL → boot wait → service wait. `checkHealth()` is quick probe only (no WOL) for tests.

### Database / Ingestion

6. **Null bytes crash PostgreSQL** — Body text with `\0` causes "invalid byte sequence" error, aborting the transaction and cascading across the batch. Fix: strip via `.replace(/\0/g, '')`. Commit: `abc1234`.

7. **Duplicate attachment hashes poison transactions** — No ON CONFLICT clause on the unique index meant one duplicate aborted the entire batch. Fix: `ON CONFLICT (attachment_hash) DO NOTHING`. Commit: `def5678`.

8. **Duplicate dedup hashes abort batch** — Same pattern as #7 but for the record dedup hash. Two records with identical date+from+subject+body_size in one batch aborted everything. Fix: `ON CONFLICT (dedup_hash) DO NOTHING`.

9. **postgres.js boolean arrays** — Require explicit `::boolean[]` cast or serialisation breaks. Commit: `b8d0360`.

10. **DB idle timeout (30s) kills connections** — Large folder discovery takes ~60s; pool closes all connections, Bun exits cleanly (code 0) with no error. Fix: batch sizes of 500+ keep connection alive. Commit: `ghi9012`.

11. **Dry run exits silently on large folders** — `showDryRun()` parsed an entire folder before calling `insertBatch()`. For 22K+ records (~60s), DB pool timed out and Bun exited code 0. Fix: dry run now processes in batches of 500.

### Source / Connectivity

12. **Resource fork sidecar files** — macOS creates `._*.ext` files on external drives that pass the extension filter. Fix: discard files starting with `._`. Commit: `5032084`.

13. **Folder names with special chars** — Brackets/spaces in names (e.g., `[Gmail]/Sent Mail`). Fix: inline env var quoting pattern in Justfile.

14. **Late error events after connection close** — Socket emits error with no listener after timeout, crashing the process. Fix: register no-op `.on('error')` before `close()`.

### Filter / Rules

15. **`sender_domain` is computed, not stored** — Derived at query time from `from_address` via `extractSenderDomain()`. Not a column — don't try to query it directly.

16. **`record_current_filter` is a VIEW** — Shows latest result per record (DISTINCT ON). The table `record_filter_results` has multiple rows per record per run.

### CLI

17. **`--limit` value misread as positional arg** — `args.find(a => !a.startsWith('--'))` picked up flag values. Fix: track flag value indices and exclude them.

## Testing

- **Framework:** bun:test
- **Location:** `test/` (11 top-level files) + `test/signals/` (8 files + subdirs)
- **Run:** `just test` (500+ tests, ~2 min)
- **No mocking** — real DB + real LLM, with `describe.skipIf(!canConnect)` for unavailable services

**Patterns:**
- Factory helpers: `makeRecord()`, `makeCondition()`, `makeRule()`, `makeSignal()`
- `afterAll(closeDb())` in all integration test files
- Unit/integration separated via comment dividers

## Commands (Quick Reference)

```bash
just download <name>    # Download a source
just ingest             # Ingest records into PostgreSQL
just filter             # Run rule-based filtering
just classify           # LLM classification (auto-wakes server)
just extract-signals    # Deterministic signal extraction
just test               # Run all tests
just db-up              # Start PostgreSQL
just llm-wake           # Wake LLM server via WOL
````

See `CLAUDE.md` for the complete 70+ recipe reference.

## How to Work Here

1. **Read CLAUDE.md** (automatic), then paste this file for deep context
2. **Check module boundaries** — never import `postgres`, `openai`, or `imapflow` directly
3. **Respect the ignore layer** — all record queries must use `notIgnoredRecord(sql)`
4. **Run tests before committing** — `just test` (tests skip gracefully if DB/LLM unavailable)
5. **Commit by logical concern** — group related changes, not chronological order
6. **Check Quirks first** — if something behaves unexpectedly, review the gotchas above

```

---

## After Generation

Once you've created `docs/BOOTSTRAP_PROMPT.md`, inform the user:

> **BOOTSTRAP_PROMPT.md created** ([X] lines, complexity tier: [simple/medium/complex]).
>
> This file provides deep architectural understanding:
> - [X] files in reading list (shared type files included: [yes/no])
> - [X] module boundaries with exports documented
> - [X] key files with API-reference quality exports
> - [X] quirks with file:line references (all known bugs: [yes/no])
> - [X] subsystem isolation descriptions in architecture
> - [Infrastructure diagram if applicable]
>
> Next: `"Read .prompts/SESSION_GENERATOR.md and create docs/SESSION_HANDOFF.md"`
```
