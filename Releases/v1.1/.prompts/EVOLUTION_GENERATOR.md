# EVOLUTION_GENERATOR.md
<!-- Version: 1.1 -->

> **What this file does:** Generates `docs/EVOLUTION.md` — a reverse-chronological log of decisions and changes. This file captures the "why" behind the project's evolution, seeded from git history.
>
> **How to run:** Prompt Claude Code with: `"Read .prompts/EVOLUTION_GENERATOR.md and create docs/EVOLUTION.md"`
>
> **Pre-requisite:** Run PROJECT_SURVEY.md first. The survey output must be in context.
>
> **Output:** Creates `docs/EVOLUTION.md` (renames existing to `EVOLUTION_old.md` if present).

---

## Instructions for Claude Code

You are about to generate `docs/EVOLUTION.md` for this project. This file is the project's memory — it records what changed, what was decided, and why.

**This file should be:**

1. **Reverse chronological** — Newest entries at the top (LLMs read top-first)
2. **Decision-focused** — Captures the "why", not just the "what"
3. **Seeded from history** — Use git commits and discovered patterns to populate initial entries
4. **Living document** — Will be appended to via EVOLUTION_MAINTENANCE.md

**Use the PROJECT_SURVEY_OUTPUT.md** (already in context) as your source of truth.

**Key principle:** EVOLUTION.md answers "How did we get here?" It's the institutional memory that prevents re-litigating past decisions.

---

## File Handling

Before creating the new file:

1. Ensure `docs/` directory exists (create if not)
2. Check if `docs/EVOLUTION.md` exists
3. If yes, rename it to `EVOLUTION_old.md`
4. If `EVOLUTION_old.md` already exists, rename to `EVOLUTION_old2.md` (and so on)
5. Create the new `docs/EVOLUTION.md`

---

## Seeding from Git History

Before writing the document, scan git history to discover:

### Commits to Look For

```bash
# Recent commits (last 50-100)
git log --oneline -50

# Commits with decision indicators
git log --grep="decision" --grep="chose" --grep="instead of" --grep="switched to" --all-match -i

# Commits with change indicators  
git log --grep="add" --grep="feature" --grep="implement" -i

# Commits with fix indicators (reveal past problems)
git log --grep="fix" --grep="bug" --grep="workaround" --grep="revert" -i

# Commits with refactor indicators (reveal architecture changes)
git log --grep="refactor" --grep="restructure" --grep="migrate" --grep="upgrade" -i
```

### What to Extract

For each significant commit or group of commits:

| Extract | Why |
|---------|-----|
| Date | When it happened |
| Type | Decision, Feature, Bugfix, Breaking Change, Refactor |
| Title | Brief description |
| Description | What changed and why |
| Files affected | Key files (not exhaustive) |

### Grouping Commits

Related commits can be grouped into a single entry:
- Multiple commits for one feature → Single "Feature" entry
- Bug + fix commits → Single "Bugfix" entry with problem and solution
- Series of refactors → Single "Refactor" entry describing the transformation

---

## Document Structure

Generate `EVOLUTION.md` with the following sections.

### Section 1: Header

```markdown
# Evolution — [Project Name]

> **What this is:** A reverse-chronological log of decisions, features, and significant changes. Newest entries at top.
>
> **How to use:** Read this after BOOTSTRAP_PROMPT.md to understand how the project evolved. Check here before proposing changes that might re-litigate past decisions.
>
> **Last updated:** [DATE]
```

---

### Section 2: Summary

A brief overview of where the project stands and key milestones.

```markdown
## Summary

- **Current phase:** [e.g., "Phase 3 — LLM Integration"]
- **Started:** [Date project began]
- **Major milestones:**
  - [Date] — [Milestone 1]
  - [Date] — [Milestone 2]
  - [Date] — [Milestone 3]
```

---

### Section 3: Changelog Entries

The main content — reverse chronological entries.

#### Entry Format

```markdown
---

### [DATE] — [Type]: [Title]

**What:** [Brief description of what changed]

**Why:** [Reasoning behind the decision or change]

**Details:**
- [Specific detail 1]
- [Specific detail 2]
- [Specific detail 3]

**Files:** `path/to/file.ts`, `path/to/other.ts`
```

#### Entry Types

| Type | Use For | Example |
|------|---------|---------|
| **Decision** | Architectural choices, technology selections | "Decision: Use Drizzle over Prisma" |
| **Feature** | New functionality added | "Feature: PDF upload with validation" |
| **Bugfix** | Problem discovered and fixed | "Bugfix: HTMX form submission breaking" |
| **Breaking** | Changes that break existing behavior | "Breaking: API response format changed" |
| **Refactor** | Code restructuring without behavior change | "Refactor: Extract LLM logic to separate module" |
| **Upgrade** | Dependency or platform upgrades | "Upgrade: Astro 4 → Astro 5" |
| **Deprecation** | Features being phased out | "Deprecation: Old auth system" |

---

### Section 4: Decision Log (Optional)

For projects with many architectural decisions, a dedicated section linking to detailed ADRs or summarizing key choices.

```markdown
## Key Decisions Index

| ID | Decision | Date | Status |
|----|----------|------|--------|
| D-001 | Use local LLM over cloud API | 2026-03-15 | Active |
| D-002 | Drizzle ORM over Prisma | 2026-03-16 | Active |
| D-003 | Store raw LLM responses | 2026-04-01 | Active |
| D-004 | ~~Use REST over tRPC~~ | 2026-03-10 | Superseded by D-005 |
| D-005 | Use simple fetch, no abstraction | 2026-03-20 | Active |
```

---

## Quality Checklist

Before presenting the generated EVOLUTION.md, verify:

- [ ] **Reverse chronological** — Newest entries at top
- [ ] **"Why" is always included** — Every entry explains reasoning
- [ ] **Types are consistent** — Using the defined type vocabulary
- [ ] **Dates are accurate** — From git history or survey
- [ ] **No trivial entries** — Skip minor commits, focus on significant changes
- [ ] **Files are relevant** — Key files only, not every file touched
- [ ] **Summary reflects current state** — Accurate phase and milestones

---

## Examples

### Example 1: New Project (~40 lines)

For a greenfield project with minimal history:

```markdown
# Evolution — url-shortener

> **What this is:** A reverse-chronological log of decisions and changes.
>
> **Last updated:** 2026-04-02

## Summary

- **Current phase:** Initial development
- **Started:** 2026-04-01
- **Major milestones:**
  - 2026-04-01 — Project created

---

### 2026-04-02 — Decision: Use pyperclip for clipboard

**What:** Chose pyperclip library for clipboard integration.

**Why:** Cross-platform support (macOS, Windows, Linux) without platform-specific code. Simpler than pyobjc for our needs.

**Alternatives considered:**
- pyobjc — macOS only, overkill for clipboard
- subprocess pbcopy — macOS only

**Files:** `src/clipboard.py`

---

### 2026-04-01 — Decision: Use Click for CLI

**What:** Chose Click library for command-line interface.

**Why:** Better developer experience than argparse. Automatic help generation, type validation, and cleaner syntax.

**Files:** `src/cli.py`, `pyproject.toml`

---

### 2026-04-01 — Feature: Project initialization

**What:** Created project structure with uv, basic CLI scaffold.

**Why:** Starting new tool for URL shortening workflow.

**Files:** `pyproject.toml`, `src/cli.py`, `src/shortener.py`
```

---

### Example 2: Mid-Development Project (~80 lines)

```markdown
# Evolution — browser-automation

> **What this is:** A reverse-chronological log of decisions and changes.
>
> **Last updated:** 2026-04-02

## Summary

- **Current phase:** Phase 4 — DOM Conversion
- **Started:** 2026-03-15
- **Major milestones:**
  - 2026-03-20 — A11y API control working
  - 2026-03-25 — Extension communication established
  - 2026-03-30 — End-to-end capture working

---

### 2026-04-02 — Bugfix: Table conversion losing alignment

**What:** HTML tables converting to markdown without proper column alignment.

**Why:** Original implementation didn't handle colspan/rowspan attributes.

**Details:**
- Tables with merged cells produced garbled output
- Fix: Track column spans, pad cells appropriately
- Still WIP for complex nested tables

**Files:** `src/utils/dom_to_markdown.py`

---

### 2026-03-30 — Feature: End-to-end DOM capture

**What:** Complete pipeline from URL to markdown working.

**Why:** Core functionality milestone — can now capture any page.

**Details:**
- Python sends URL to extension via native messaging
- Extension injects content script, captures DOM
- DOM returned to Python, converted to markdown
- Tested on 20+ sites successfully

**Files:** `src/orchestrator/controller.py`, `extension/background.js`, `extension/content.js`

---

### 2026-03-26 — Decision: Markdown output over JSON

**What:** Chose markdown as primary output format instead of JSON.

**Why:** More human-readable for verification. Easier to spot extraction errors. Can always parse to JSON later if needed.

**Alternatives considered:**
- JSON — Structured but hard to visually verify
- HTML — Redundant, we're capturing from HTML

**Files:** `src/utils/dom_to_markdown.py`

---

### 2026-03-25 — Decision: Brave only, not Chrome

**What:** Officially supporting only Brave browser, not Chrome.

**Why:** Reduces testing matrix. Brave is primary browser. Chrome likely works but untested.

**Details:**
- Bundle ID detection for `com.brave.Browser`
- Chrome support possible but not guaranteed

**Files:** `src/orchestrator/detector.py`

---

### 2026-03-22 — Bugfix: PyObjC import order crash

**What:** App crashing on launch with cryptic PyObjC errors.

**Why:** Must import `AppKit` before `ApplicationServices`. Python/ObjC bridge has initialization order dependencies.

**Details:**
- Symptom: `objc.error: NSInvalidArgumentException`
- Root cause: ObjC runtime not initialized in correct order
- Fix: Reorder imports, add comment warning

**Files:** `src/orchestrator/accessibility.py:1-5`

---

### 2026-03-20 — Feature: A11y API browser control

**What:** Can control Brave windows via macOS Accessibility API.

**Why:** Undetectable automation — no CDP signatures, no Playwright traces.

**Details:**
- Window focus, tab switching, URL navigation
- Uses PyObjC for Accessibility API access
- Requires permission grant in System Preferences

**Files:** `src/orchestrator/accessibility.py`, `src/orchestrator/controller.py`

---

### 2026-03-15 — Decision: A11y API over CDP/Playwright

**What:** Chose macOS Accessibility API for browser control instead of Chrome DevTools Protocol.

**Why:** Core requirement is "undetectable". CDP and Playwright leave detectable traces. A11y API controls the browser like a human would.

**Tradeoffs:**
- Pro: Truly undetectable
- Pro: Works with any browser
- Con: macOS only
- Con: More complex implementation

**Files:** Initial architecture decision
```

---

### Example 3: Mature Project (~120 lines)

```markdown
# Evolution — tax-pipeline

> **What this is:** A reverse-chronological log of decisions and changes.
>
> **Last updated:** 2026-04-02

## Summary

- **Current phase:** Phase 3 — LLM Integration (75%)
- **Started:** 2026-03-10
- **Major milestones:**
  - 2026-03-15 — Infrastructure complete (Docker, DB, Auth)
  - 2026-03-22 — PDF upload and parsing working
  - 2026-03-28 — Basic LLM extraction working
  - 2026-04-05 — Target: Schema matching complete

## Key Decisions Index

| ID | Decision | Date | Status |
|----|----------|------|--------|
| D-001 | Local LLM over cloud API | 2026-03-12 | Active |
| D-002 | Drizzle over Prisma | 2026-03-10 | Active |
| D-003 | Astro SSR over Next.js | 2026-03-10 | Active |
| D-004 | Store raw LLM responses | 2026-04-01 | Active |
| D-005 | Confidence threshold 0.7 | 2026-04-01 | Active |

---

### 2026-04-02 — Decision: Flatten before schema matching

**What:** Flatten nested LLM JSON output before attempting to match to schema.

**Why:** Matching logic is simpler with flat key-value pairs. Array items become `items.0.description`, `items.1.description`, etc.

**Details:**
- Recursive flattening preserves array indices
- Reconstruction happens after matching
- Easier to debug — can see all fields at once

**Files:** `src/llm/schema-matcher.ts`

---

### 2026-04-01 — Decision: Store raw LLM responses (D-004)

**What:** Save the raw JSON from LLM alongside processed results.

**Why:** Enables reprocessing without re-calling LLM. Essential for debugging extraction issues. LLM calls are slow and expensive (time, not money).

**Details:**
- New `raw_response` column in extractions table
- ~10KB per extraction on average
- Worth the storage cost for debugging value

**Files:** `src/db/schema.ts`, `src/llm/extractor.ts`

---

### 2026-04-01 — Decision: Confidence threshold 0.7 (D-005)

**What:** Extractions below 0.7 confidence flagged for human review.

**Why:** Balance between automation and accuracy. 0.7 catches most errors without excessive false positives.

**Details:**
- Tested on 50 sample documents
- 0.7 threshold: 92% accuracy, 15% flagged for review
- 0.8 threshold: 96% accuracy, 35% flagged (too many)
- 0.6 threshold: 85% accuracy, 8% flagged (too few catches)

**Files:** `src/llm/extractor.ts`, `src/schemas/extraction.ts`

---

### 2026-03-30 — Bugfix: HTMX file upload breaking

**What:** File uploads via HTMX forms failing silently.

**Why:** `hx-boost` intercepts form submission, breaks multipart/form-data encoding.

**Details:**
- Symptom: Files not reaching server, no error shown
- Root cause: HTMX boost converts to AJAX, loses file data
- Fix: Add `hx-boost="false"` to file upload forms

**Files:** `src/components/UploadForm.tsx`

---

### 2026-03-28 — Feature: Basic LLM extraction

**What:** Can extract structured data from PDF text using Qwen 3.

**Why:** Core functionality — turn unstructured PDFs into queryable data.

**Details:**
- Uses Qwen 3 8B via LM Studio
- Few-shot prompting with 3 examples
- `/no_think` suffix essential for clean output
- ~5 second extraction time after model loaded

**Files:** `src/llm/extractor.ts`, `src/llm/prompts.ts`

---

### 2026-03-25 — Bugfix: First LLM request timeout

**What:** First request to LLM server timing out.

**Why:** LM Studio JIT loads models on first request. 10-30 second load time exceeds default timeout.

**Details:**
- Symptom: First extraction fails, subsequent work
- Root cause: Model not loaded until first request
- Fix: Increase timeout to 60s, add retry logic
- Also documented in CLAUDE.md gotchas

**Files:** `src/llm/client.ts`

---

### 2026-03-22 — Feature: PDF parsing pipeline

**What:** Can extract text and images from uploaded PDFs.

**Why:** Prerequisite for LLM extraction — need to get content out of PDFs first.

**Details:**
- pdfjs-dist for text extraction
- Sharp for image processing
- Handles both text-based and scanned PDFs
- Images extracted for vision model fallback

**Files:** `src/pdf/parser.ts`, `src/pdf/images.ts`

---

### 2026-03-15 — Feature: Infrastructure complete

**What:** Docker, PostgreSQL, authentication all working.

**Why:** Foundation for application development.

**Details:**
- PostgreSQL 16 in Docker
- Better Auth with Drizzle adapter
- Session management working
- All migrations applied

**Files:** `docker-compose.yml`, `src/db/schema.ts`, `src/auth/config.ts`

---

### 2026-03-12 — Decision: Local LLM over cloud API (D-001)

**What:** Use local LLM inference (LM Studio on RTX 3090) instead of OpenAI/Anthropic APIs.

**Why:** Cost control (no per-token charges), privacy (tax documents stay local), no rate limits, works offline.

**Tradeoffs:**
- Pro: Zero ongoing cost after hardware
- Pro: Complete data privacy
- Pro: No rate limits
- Con: Lower capability than GPT-4/Claude
- Con: Requires separate machine with GPU
- Con: Model management overhead

**Files:** Architecture decision, `.env` configuration

---

### 2026-03-10 — Decision: Tech stack selection (D-002, D-003)

**What:** Chose Astro 5 + Drizzle + PostgreSQL + Better Auth.

**Why:** 
- Astro: SSR with islands, simpler than Next.js for this use case
- Drizzle: Type-safe, lighter than Prisma, better DX
- PostgreSQL: Robust, good tooling, familiar
- Better Auth: Simple, works with Drizzle

**Alternatives considered:**
- Next.js — More complex than needed
- Prisma — Heavier, slower migrations
- SQLite — Less robust for concurrent access

**Files:** Initial project setup
```

---

## After Generation

Once you've created `docs/EVOLUTION.md`, inform the user:

> **EVOLUTION.md created** ([X] lines).
>
> This file captures the project's evolution:
> - [X] entries spanning [date range]
> - Key decisions: [list 2-3 major decisions]
> - Recent activity: [most recent entry type and topic]
>
> **All 4 generated files are now complete:**
> - ✅ CLAUDE.md
> - ✅ docs/BOOTSTRAP_PROMPT.md
> - ✅ docs/SESSION_HANDOFF.md
> - ✅ docs/EVOLUTION.md
>
> Next steps:
> 1. Review the generated files
> 2. Commit them to git
> 3. Exit/clear this session (context is "spent")
> 4. In future sessions, paste BOOTSTRAP → EVOLUTION → SESSION_HANDOFF after CLAUDE.md auto-loads
