# CLAUDE_GENERATOR.md

<!-- Version: 2.2 -->

> **What this file does:** Generates a complete, rules-focused `CLAUDE.md` file for the project root. This is the file Claude Code reads automatically at session start.
>
> **How to run:** Prompt Claude Code with: `"Read .prompts/CLAUDE_GENERATOR.md and create CLAUDE.md"`
>
> **Pre-requisite:** Run PROJECT_SURVEY.md first. The survey output must be in context.
>
> **Output:** Creates `CLAUDE.md` in the project root (renames existing to `CLAUDE_old.md` if present).

---

## Instructions for Claude Code

You are about to generate `CLAUDE.md` for this project. This file will be read automatically by Claude Code at the start of every session, so it must be:

1. **Complete** — Every command, env var, and rule a developer needs each session
2. **Scannable** — Tables, categorized sections, no prose paragraphs
3. **Rules-focused** — What to do, what not to do, actionable reference
4. **Right-sized** — Length follows project complexity (see Content Allocation Guide below)
5. **Non-duplicative** — Architecture, reading lists, and "why" explanations go in BOOTSTRAP_PROMPT.md, not here

**Use the PROJECT_SURVEY_OUTPUT.md** (already in context) as your source of truth.

> **The cardinal rule:** A new Claude Code session must be able to construct any project command, reference any env var, and know every module boundary from CLAUDE.md alone, without reading any other file. If removing a line would require the next session to open a source file to get the information, keep the line.

---

## Content Allocation Guide

Before writing, assess the project's complexity from the survey output.

### Step 1 — Complexity Assessment

Score the project on three axes:

| Axis           | Simple         | Medium                     | Complex                      |
| -------------- | -------------- | -------------------------- | ---------------------------- |
| Commands       | < 10           | 10-30                      | 30+                          |
| Modules        | 1-2            | 3-5                        | 6+                           |
| Infrastructure | Single machine | Docker or 1 remote service | Multi-machine, multi-service |

The **highest** axis determines the tier. A project with 40 commands but 2 modules is Complex (because commands are 30+).

### Step 2 — Line Budget

| Tier    | Target Range  | Hard Maximum |
| ------- | ------------- | ------------ |
| Simple  | 40-80 lines   | 100          |
| Medium  | 80-150 lines  | 180          |
| Complex | 150-250 lines | 300          |

### Step 3 — Space Allocation (approximate %)

| Section           | Simple  | Medium  | Complex |
| ----------------- | ------- | ------- | ------- |
| Title + One-liner | 3 lines | 3 lines | 3 lines |
| Commands          | 30%     | 30%     | 25%     |
| Tech Stack        | 10%     | 8%      | 6%      |
| Env Var Reference | skip    | 10%     | 12%     |
| Rules             | 25%     | 25%     | 25%     |
| Infrastructure    | skip    | 10%     | 12%     |
| Project Structure | 10%     | 8%      | 8%      |
| Conventions       | 10%     | 5%      | 4%      |
| Further Reading   | 5%      | 4%      | 3%      |

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

**Good:** "A 5-stage CLI pipeline that extracts financial data from 156K emails across 5 IMAP mailboxes for Australian tax preparation, using a local LLM for classification and PostgreSQL for storage."

**Bad:** "A data processing pipeline for emails."

---

### Section 2: Commands

List commands the developer will use. Categorize when there are more than 10.

**Inclusion rule — three tiers:**

- **Always include:** Commands used weekly or more (core pipeline, testing, infrastructure, status checks)
- **Include if space allows:** Commands used monthly (rollback, export/import, diagnostics, one-off queries)
- **Exclude:** One-time setup commands (initial migration, seed, one-time data fixes) — mention in BOOTSTRAP only

**When in doubt, include.** A missing command costs 2-5 minutes finding and reading the Justfile/Makefile. An extra line costs < 1 token per session.

**For <10 commands:**

````markdown
## Commands

```bash
just dev          # Start dev server
just test         # Run all tests
just db-up        # Start PostgreSQL container
```
````

````

**For 10+ commands, categorize:**
```markdown
## Commands

### Pipeline
```bash
just download <name>       # Download a mailbox
just ingest                # Ingest emails into PostgreSQL
just filter                # Filter emails using rules
````

### Infrastructure

```bash
just test                  # Run all tests
just db-up                 # Start PostgreSQL container
just db-migrate            # Run database migrations
```

````

**For 30+ commands, use 4-6 categories.** Include ALL commands in every category — do not summarize with "and others" or "etc."

**Source commands from:** Justfile, Makefile, package.json scripts, pyproject.toml scripts, or deno.json tasks.

---

### Section 3: Tech Stack

Table format, one row per layer. Include only what's actively used.

```markdown
## Tech Stack

| Layer | Technology |
|-------|------------|
| Runtime | Bun |
| Language | TypeScript 5 (strict) |
| Database | PostgreSQL 16 (Docker, port 10045) |
| Testing | bun:test (512 tests) |
````

---

### Section 4: Env Var Reference (Conditional)

**Include this section if:** The project uses inline environment variables with a task runner (Justfile, Makefile) where variable names, applicable commands, and meanings are not obvious from the command itself.

**Skip this section if:** The project has no env var pattern, or env vars are only used in `.env` for configuration (not inline on commands).

**Include ALL env vars** that modify command behavior. This table is the #1 reference a developer needs to construct commands correctly. If a variable is documented in the Justfile comments or the survey, it belongs here.

````markdown
## Env Var Reference

Values with special characters use inline env vars before the command:

```bash
FOLDER="[Gmail]/Sent Mail" just download tash-gmail-tsrah
FY=FY2024-25 OWNER=gavin just filter
RUN_ID=abc-123 just ingest-rollback
```
````

| Variable  | Recipes                            | Meaning                         |
| --------- | ---------------------------------- | ------------------------------- |
| `FOLDER`  | download, ingest                   | Single folder name              |
| `FOLDERS` | download, ingest                   | Comma-separated folder names    |
| `FY`      | ingest, filter                     | Financial year (e.g. FY2024-25) |
| `RUN_ID`  | ingest-rollback, classify-rollback | Run UUID                        |

````

---

### Section 5: Rules

Split into three categories, plus an optional fourth. Be specific and actionable.

**Boundaries:** Include ALL module ownership rules discovered in the survey. Do not cap at a fixed number. If the project has 8 boundaries, list all 8. Each boundary should name the file/module, what it owns, and what violation looks like.

**Patterns:** Include conventions that affect how code is written. Keep each to one line.

**Gotchas:** Each gotcha MUST include the fix or workaround, not just the problem description.

**Domain Conventions (optional):** Include if the project has domain-specific patterns that affect data formatting, parsing, or display (date formats, currency, naming conventions, ID formats).

```markdown
## Rules

### Boundaries — Never Cross These

1. **`src/lib/db.ts`** — sole importer of `postgres`. All DB access goes through here.
2. **`src/lib/llm.ts`** — sole importer of `openai`. All LLM calls go through here.
3. **`src/lib/sanitise.ts`** — sole sanitiser for filenames/paths. Never sanitise elsewhere.

### Patterns — Follow These

1. Env vars via `process.env` (not `import.meta.env`)
2. File paths via `path.join()`, never hardcode separators
3. Integration tests use `describe.skipIf(!canConnect)` — skip, never fail

### Gotchas — Things That Have Bitten Us

1. **Null bytes in email body** — strip via `.replace(/\0/g, '')` before INSERT
2. **LM Studio bug #1602** — `content` can be empty; fall back to `reasoning_content`
3. **DB idle timeout (30s)** — batch sizes of 500+ keep connection alive

### Domain Conventions

- Dates: Australian DD/MM/YYYY, financial year Jul 1 → Jun 30 (`FY2024-25`)
- Currency: AUD patterns (`$1,234.56`, `-$50`, `($50)`, CR suffix → negative)
````

---

### Section 6: Infrastructure (Conditional)

**Include this section if:** The project involves multiple machines, Docker services, remote APIs with specific connection details, or services with precise configuration (model IDs, port numbers, quant formats).

**Skip if:** Single-machine project with no external services.

The section has three sub-components:

1. **Machine/service table** — machines, roles, addresses
2. **Service details** — verbatim model IDs, quant naming conventions, VRAM requirements, port numbers. These are exact strings that must be correct or calls fail silently.
3. **Operational notes** — auth model, auto-wake, constraints, storage conventions

**Model IDs must be verbatim.** If the survey lists specific model strings (e.g., `qwen/qwen3.5-27b` vs `qwen3.5-27b@q4_k_s`), include them exactly. Incorrect model IDs cause silent failures.

```markdown
## Infrastructure

| Machine     | Role                      | Address           |
| ----------- | ------------------------- | ----------------- |
| Mac Mini M4 | Dev machine (Claude Code) | localhost         |
| RTX 3090 PC | LLM inference (LM Studio) | 192.168.1.56:1234 |
| Docker      | PostgreSQL 16             | localhost:10045   |

**LLM Models (verbatim IDs):**

- `qwen/qwen3.5-27b` — primary (best accuracy)
- `qwen3.5-27b@q4_k_s` — alt quant (17.6 GB, ~21 GB VRAM)
- `qwen/qwen3.5-9b` — fallback (faster)

**Quant naming:** `qwen/` prefix for official, `model@quant` for third-party

**Operational:**

- No auth on LLM API — security via LAN isolation
- One model at a time — requesting different model auto-evicts current
- First request per model: 10-30s (JIT loading)
- Data stored on external drives — paths via env vars, never hardcoded
```

---

### Section 7: Project Structure

Show the directory tree to the depth that reveals module architecture. Include:

- Every top-level source directory with a one-line summary
- Subdirectories with file counts and purpose
- Individual files ONLY if they are critical entry points, schema definitions, or have special significance (e.g., "this is the only file that imports X")

Do **not** list every individual file — that's derivable from `ls`. Do **not** compress to a single line per major directory — that loses module architecture.

**Target:** ~10-20 lines for complex projects, ~5-8 for simple.

```markdown
## Project Structure
```

src/
├── config/ # 2 files — dynamic config from .env (types.ts, config.ts)
├── db/ # 2 files — schema.sql (649 lines) + migrate.ts
├── lib/ # 14 core modules
│ ├── db.ts # DB client + ignore layer (sole postgres importer)
│ ├── llm.ts # LLM client + WOL (sole OpenAI SDK importer)
│ ├── filter-engine.ts # Rule-based email filter (10+ operators)
│ ├── classifier.ts # LLM classification with retry/resume/rollback
│ └── ... # imap, email-store, sanitise, json-parse, etc.
├── signals/ # 7 files — extraction pipeline: patterns → labeler → aggregator
└── scripts/ # 9 CLI entry points (download, ingest, filter, classify, etc.)
test/ # 24 test files (11 top-level + signals/ subdir)

```

```

---

### Section 8: Conventions

Brief notes on project conventions.

```markdown
## Conventions

- **Default branch:** `master`
- **Runner:** `bun run` for scripts
- **Python (if any):** `uv run python3`
- **Commits:** Logical grouping by step/concern, not chronological
- **No PRs:** Single developer, direct commits to master
```

---

### Section 9: Further Reading

Reference other documentation for deeper context. Only include files that exist.

```markdown
## Further Reading

- **Deep onboarding:** `docs/BOOTSTRAP_PROMPT.md` — architecture, module boundaries, quirks
- **Current state:** `docs/SESSION_HANDOFF.md` — what's in progress, what's next
- **History:** `docs/EVOLUTION.md` — decisions made, features added
```

---

## Cross-Reference Guide — What Goes Where

When deciding whether content belongs in CLAUDE.md or another generated file, use this table:

| Content Type                     | CLAUDE.md         | BOOTSTRAP               | SESSION      | EVOLUTION |
| -------------------------------- | ----------------- | ----------------------- | ------------ | --------- |
| Command reference (all commands) | YES               | Quick ref only          | —            | —         |
| Env var reference table          | YES               | —                       | —            | —         |
| Module boundaries (list)         | YES               | Detailed table          | —            | —         |
| Gotchas with fixes               | YES (1-line each) | Detailed with file:line | —            | —         |
| Model IDs / service details      | YES               | —                       | —            | —         |
| Architecture explanation         | —                 | YES                     | —            | —         |
| Reading list (files to read)     | —                 | YES                     | —            | —         |
| "Why" behind decisions           | —                 | YES                     | —            | YES       |
| Current state / in-progress work | —                 | —                       | YES          | —         |
| Decision history                 | —                 | —                       | —            | YES       |
| Data flow diagrams               | —                 | YES                     | —            | —         |
| Debugging playbook               | —                 | YES                     | —            | —         |
| Operational workflows            | —                 | YES                     | Current step | —         |
| Domain conventions (formats)     | YES (brief)       | YES (detailed)          | —            | —         |
| Storage conventions / guardrails | YES               | YES (detailed)          | —            | —         |

**The litmus test:** If a new session needs this information to execute a command correctly or avoid breaking something, it belongs in CLAUDE.md. If it helps understand _why_ something works that way, it belongs in BOOTSTRAP.

---

## Quality Checklist

Before presenting the generated CLAUDE.md, verify:

**Completeness**

- [ ] Could a new session construct ANY `just`/`make` command from this file alone?
- [ ] Are ALL module ownership boundaries listed (not just "top 3-5")?
- [ ] Are ALL env vars that modify command behavior in the reference table?
- [ ] Are service-specific details verbatim (model IDs, ports, quant names)?

**Right-sizing**

- [ ] Line count is within the target range for this project's complexity tier?
- [ ] Would removing any line require a developer to open a source file?
- [ ] No section is padded with explanatory prose that could be a table row?

**Accuracy**

- [ ] Commands are pulled from actual Justfile/Makefile/package.json?
- [ ] Gotchas include the fix/workaround, not just the problem?
- [ ] Rules are specific ("never do X in Y") not vague ("be careful with X")?

**Format**

- [ ] Tables used for structured data (3+ items with multiple attributes)?
- [ ] No empty sections — omit sections that don't apply?

---

## Examples

### Example 1: Minimal CLAUDE.md (~25 lines)

For a simple project with few rules:

````markdown
# url-shortener

A CLI tool that shortens URLs using the is.gd API and tracks click counts in SQLite.

## Commands

```bash
uv run python3 -m urlshort <url>    # Shorten a URL
uv run python3 -m urlshort --list   # List all shortened URLs
uv run pytest                        # Run tests
```
````

## Rules

### Boundaries

1. **`src/api.py`** — sole HTTP client. Never call `requests` elsewhere.

### Gotchas

1. **is.gd rate limit** — max 10 requests/minute; `api.py` has built-in throttle

## Conventions

- **Default branch:** `master`
- **Runner:** `uv run python3`

````

---

### Example 2: Medium CLAUDE.md (~90 lines)

For a browser automation project with env vars:

```markdown
# browser-automation

Captures any web page's DOM as markdown using macOS Accessibility API and a Chrome MV3 extension — fully undetectable (no CDP, no Playwright).

## Commands

### Core
```bash
just run                    # Start the orchestrator
just capture <url>          # Capture a single page
just batch                  # Process URL list (env var: FILE)
````

### Extension

```bash
just extension-build        # Build the Chrome extension
just extension-install      # Install to Brave (dev mode)
just extension-reload       # Hot reload after changes
```

### Testing

```bash
just test                   # Run all tests
just test-e2e               # End-to-end browser tests
```

## Tech Stack

| Layer           | Technology                       |
| --------------- | -------------------------------- |
| Runtime         | Python 3.12                      |
| Browser Control | macOS Accessibility API (PyObjC) |
| Extension       | Chrome MV3 (Manifest V3)         |
| Testing         | pytest                           |

## Env Var Reference

| Variable  | Recipes        | Meaning                                     |
| --------- | -------------- | ------------------------------------------- |
| `FILE`    | batch          | Path to URL list file                       |
| `TIMEOUT` | capture, batch | Page load timeout in seconds (default: 30)  |
| `FORMAT`  | capture, batch | Output format: `md` or `html` (default: md) |

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

1. **PyObjC import order** — must import `AppKit` before `ApplicationServices` or segfault
2. **Accessibility permissions** — app must be granted in System Preferences > Privacy
3. **MV3 service workers** — no persistent state, use `chrome.storage`
4. **Brave detection** — bundle ID is `com.brave.Browser`, not `com.google.Chrome`

## Conventions

- **Default branch:** `master`
- **Runner:** `uv run python3`
- **Browser:** Brave only (Chrome works but untested)

````

---

### Example 3: Complex CLAUDE.md (~220 lines)

For a multi-machine pipeline project with many commands and env vars:

```markdown
# tax-data-sprint

A 5-stage CLI pipeline that extracts financial data from 156K emails across 5 IMAP mailboxes for Australian tax preparation, using a local LLM (Qwen 3.5 on RTX 3090) for classification and PostgreSQL for storage.

## Commands

### Pipeline
```bash
just download <name>         # Download a mailbox (env vars: FOLDER, FOLDERS, EXCLUDE, SINCE)
just download-plan <name>    # Dry run — show download plan
just download-status <name>  # Show download state
just download-user <user>    # Download all mailboxes for a user
just download-report         # Download progress across all mailboxes
just ingest                  # Ingest emails into PostgreSQL (env vars: MAILBOX, FOLDER, SINCE, UNTIL, FY, LIMIT)
just ingest-plan             # Dry run — show what would be ingested
just ingest-status           # Show ingestion status from DB
just ingest-rollback         # Undo a run (env var: RUN_ID)
just filter                  # Filter emails (env vars: MAILBOX, OWNER, SINCE, UNTIL, FY)
just filter-plan             # Dry run — show what would be filtered
just filter-status           # Show filter results summary
just classify                # Classify uncertain emails using LLM (auto-wakes server)
just classify-plan           # Dry run — show what would be classified
just classify-status         # Show classification run status
just classify-rollback       # Rollback a run (env var: RUN_ID)
just extract-signals         # Extract signals from included emails
just extract-signals-plan    # Dry run — preview extraction
just signals-report          # Show extraction statistics
````

### Rules & Review

```bash
just rules-list [args]       # List rules (--enabled, --action, --sort hits)
just rules-add [args]        # Add rule (--name, --action, --category, --condition)
just rules-show <id>         # Show rule details
just rules-edit <id> [args]  # Edit a rule
just rules-delete <id>       # Delete a rule
just rules-disable <id>      # Disable a rule
just rules-enable <id>       # Enable a rule
just rules-propose           # Propose rules from pattern analysis
just rules-test [args]       # Test a rule without adding
just rules-export            # Export rules to JSON (env var: FILE)
just rules-import            # Import rules from JSON (env var: FILE)
just rules-health            # Rule health report
just classify-review         # Preview emails needing human review
just classify-proposed-rules # Show LLM-proposed rules
```

### Data & Diagnostics

```bash
just search                  # Full-text search (env var: QUERY)
just verify-ingest           # Post-ingest verification report
just ingest-ignore [args]    # Add to ignore list (--message-id, --dedup-hash, etc.)
just ingest-unignore [args]  # Remove from ignore list
just ingest-ignore-list      # Show all ignored emails/attachments
just signals-show            # Show signals for email (env var: EMAIL_ID)
just signals-export [args]   # Export signals (--format csv|json, --strong-only)
just counterparty-list       # List known counterparties
just counterparty-add [args] # Add counterparty (--name, --abn, --domain)
```

### Infrastructure

```bash
just test                    # Run all tests (512 tests, bun:test)
just check                   # Type check
just db-up                   # Start PostgreSQL container
just db-migrate              # Run database migrations
just db-shell                # Open psql shell
just llm-health              # Check LLM server
just llm-models              # List available models
just llm-wake                # Wake LLM server via WOL
just config-validate         # Validate env vars + print summary
just mailbox-check [name]    # Test IMAP connectivity
```

## Tech Stack

| Layer       | Technology                                                        |
| ----------- | ----------------------------------------------------------------- |
| Runtime     | Bun                                                               |
| Language    | TypeScript 5 (strict, noUncheckedIndexedAccess, no `any`)         |
| Database    | PostgreSQL 16 Alpine (Docker, port 10045)                         |
| DB Client   | postgres.js (singleton via `db.ts`)                               |
| LLM         | OpenAI SDK → LM Studio (Qwen 3.5-27b primary, 9b fallback)        |
| Testing     | bun:test (512 tests, no mocking, skipIf for unavailable services) |
| Task Runner | Justfile (70+ recipes)                                            |

## Env Var Reference

Values with special characters use inline env vars before the command:

```bash
FOLDER="[Gmail]/Sent Mail" just download tash-gmail-tsrah
FY=FY2024-25 OWNER=gavin just filter
RUN_ID=abc-123 just ingest-rollback
```

| Variable   | Recipes                                            | Meaning                         |
| ---------- | -------------------------------------------------- | ------------------------------- |
| `FOLDER`   | download, ingest                                   | Single folder name              |
| `FOLDERS`  | download, ingest                                   | Comma-separated folder names    |
| `EXCLUDE`  | download                                           | Folder to exclude               |
| `SINCE`    | download, ingest, filter                           | Start date (ISO)                |
| `UNTIL`    | ingest, filter                                     | End date (ISO)                  |
| `MAILBOX`  | ingest, filter                                     | Mailbox name                    |
| `OWNER`    | filter                                             | User name (gavin, tash)         |
| `LIMIT`    | ingest                                             | Max emails to process           |
| `FY`       | ingest, filter                                     | Financial year (e.g. FY2024-25) |
| `QUERY`    | search                                             | Full-text search query          |
| `RUN_ID`   | ingest-rollback, classify-rollback, filter-inspect | Run UUID                        |
| `FILE`     | rules-import, rules-export                         | JSON file path                  |
| `EMAIL_ID` | signals-show                                       | Email ID number                 |

## Rules

### Boundaries — Never Cross These

1. **`src/lib/db.ts`** — sole importer of `postgres`. All DB access goes through here.
2. **`src/lib/llm.ts`** — sole importer of `openai`. All LLM calls go through here.
3. **`src/lib/json-parse.ts`** — sole JSON parser for LLM output.
4. **`src/config/config.ts`** — sole reader of user/mailbox env vars.
5. **`src/lib/sanitise.ts`** — sole sanitiser for filenames/paths.
6. **Ignore layer** — all queries must use `notIgnoredEmail(sql)` / `notIgnoredAttachment(sql)`.
7. **No `any` types.** TypeScript strict mode everywhere.
8. **Secrets** live in `.env` (gitignored). Never commit credentials.

### Patterns — Follow These

1. Env vars via `process.env` (not `import.meta.env`)
2. File paths via `path.join()`, never hardcode separators
3. Integration tests: `describe.skipIf(!canConnect)` — skip, never fail
4. Config hierarchy: `USERS` → `{USER}_MAILBOX_COUNT` → `{USER}_MAILBOXn_NAME`

### Gotchas — Things That Have Bitten Us

1. **Qwen 3.5 thinking mode** — always send `chat_template_kwargs: { enable_thinking: false }` for JSON extraction
2. **LM Studio bug #1602** — `content` can be empty; fall back to `reasoning_content`
3. **Max tokens must be 16000** for Qwen 3.x (reasoning overhead); lower truncates silently
4. **Null bytes in email body** — strip via `.replace(/\0/g, '')` before INSERT
5. **postgres.js boolean[]** — requires explicit `::boolean[]` cast
6. **macOS resource forks** — `._*.eml` files pass `.eml` filter; discard `._` prefix
7. **Gmail IMAP folders** — brackets/spaces (`[Gmail]/Sent Mail`); use inline env var quoting
8. **DB idle timeout (30s)** — batch sizes of 500+ keep connection alive
9. **`sender_domain`** — computed at query time, not a stored column
10. **`email_current_filter`** — VIEW (latest result); `email_filter_results` has multiple rows

### Domain Conventions

- Dates: Australian DD/MM/YYYY, financial year Jul 1 → Jun 30 (`FY2024-25`)
- Currency: AUD patterns (`$1,234.56`, `-$50`, `($50)`, CR suffix → negative)
- ABN: 11-digit Australian Business Number, validated via modulus 89

## Infrastructure

| Machine     | Role                      | Address           |
| ----------- | ------------------------- | ----------------- |
| Mac Mini M4 | Dev machine (Claude Code) | localhost         |
| RTX 3090 PC | LLM inference (LM Studio) | 192.168.1.56:1234 |
| Docker      | PostgreSQL 16 only        | localhost:10045   |

**LLM Models (verbatim IDs):**

- `qwen/qwen3.5-27b` — primary, best accuracy
- `qwen3.5-27b@q4_k_s` — alt quant (17.6 GB, ~21 GB VRAM)
- `qwen3.5-27b@iq4_xs` — alt quant (16.8 GB, ~20 GB VRAM)
- `qwen/qwen3.5-9b` — fallback, faster
- `qwen/qwen3.5-35b-a3b` — MoE (35B params, 3B active)
- `qwen/qwen2.5-vl-7b` — vision model
- `text-embedding-nomic-embed-text-v1.5` — embeddings

**Quant naming:** `qwen/` prefix for official, `model@quant` for third-party (no prefix)

**Operational:**

- No auth on LLM API — security via LAN isolation
- One model loaded at a time — different model auto-evicts current
- First request per model: 10-30s (JIT loading), subsequent <2s
- WOL auto-wake: `ensureServerReady()` handles ping → WOL → boot → service wait
- `checkHealth()` is quick probe only (no WOL) — used by tests to skip gracefully
- Data stored on external drives — paths via env vars, never hardcoded
- Emails stored as .eml files — never modified after download
- Download state in state.json per mailbox — enables resume

## Project Structure

```
src/
├── config/           # 2 files — dynamic config from .env (types.ts, config.ts)
├── db/               # 2 files — schema.sql (649 lines, 13 tables) + migrate.ts
├── lib/              # 14 core modules
│   ├── db.ts             # DB client + ignore layer (sole postgres importer)
│   ├── llm.ts            # LLM client + WOL (sole OpenAI SDK importer)
│   ├── json-parse.ts     # Robust JSON parsing for LLM output
│   ├── filter-engine.ts  # Rule-based email filter (10+ operators)
│   ├── classifier.ts     # LLM classification with retry/resume/rollback
│   └── ...               # imap-client, email-store, sanitise, rule-proposer, etc.
├── signals/          # 7 files + subdirs — patterns → labeler → aggregator → counterparty
└── scripts/          # 9 CLI entry points
test/                 # 24 test files (11 top-level + signals/ subdir), 512 tests
```

## Conventions

- **Default branch:** `master`
- **Runner:** `bun run` for scripts
- **Python (if any):** `uv run python3`
- **Commits:** Logical grouping by step/concern, not chronological
- **No PRs:** Single developer, direct commits to master

```

---

## After Generation

Once you've created `CLAUDE.md`, inform the user:

> **CLAUDE.md created** ([X] lines, complexity tier: [simple/medium/complex]).
>
> This file will be read automatically by Claude Code at session start. It contains:
> - [X] commands in [Y] categories
> - [X] boundary rules, [X] patterns, [X] gotchas
> - [Env var reference with X variables (if applicable)]
> - [Infrastructure with X machines (if applicable)]
>
> Next: `"Read .prompts/BOOTSTRAP_GENERATOR.md and create docs/BOOTSTRAP_PROMPT.md"`
```
