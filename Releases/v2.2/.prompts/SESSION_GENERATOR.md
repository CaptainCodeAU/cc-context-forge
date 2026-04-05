# SESSION_GENERATOR.md
<!-- Version: 2.2 -->

> **What this file does:** Generates `docs/SESSION_HANDOFF.md` for session continuity. This file captures current state, what's in progress, strategic choices ahead, and critical reminders — everything Claude needs to pick up where you left off.
>
> **How to run:** Prompt Claude Code with: `"Read .prompts/SESSION_GENERATOR.md and create docs/SESSION_HANDOFF.md"`
>
> **Pre-requisite:** Run PROJECT_SURVEY.md first. The survey output must be in context.
>
> **Output:** Creates `docs/SESSION_HANDOFF.md` (renames existing to `SESSION_HANDOFF_old.md` if present).
>
> **Companion docs:** This file works alongside EVOLUTION.md (backward-looking decisions log). SESSION_HANDOFF owns *forward-looking* state: where we are, what's next, which choices are open. EVOLUTION.md owns *backward-looking* history: what was decided and why. Don't duplicate — cross-reference.

---

## Instructions for Claude Code

You are about to generate `docs/SESSION_HANDOFF.md` for this project. This file is the "state snapshot" — it tells the next Claude session exactly where things stand right now.

**This file should be:**

1. **Current** — Reflects the actual state of the project right now
2. **Actionable** — Clear next steps, not vague plans
3. **Focused** — Only volatile state; stable architecture is in BOOTSTRAP_PROMPT.md
4. **Brief** — 50-100 lines typically; this gets updated frequently

**Use the PROJECT_SURVEY_OUTPUT.md** (already in context) as your source of truth.

**Key principle:** Three docs, three questions:
- **BOOTSTRAP_PROMPT.md** → "How does this work?" (architecture, stable)
- **SESSION_HANDOFF.md** → "Where are we now and what's next?" (volatile state + forward-looking choices)
- **EVOLUTION.md** → "How did we get here?" (backward-looking decisions)

Keep them separate. SESSION_HANDOFF is the only doc that looks *forward* — it owns open strategic choices, upcoming forks, and immediate next steps. This is its primary differentiator.

---

## File Handling

Before creating the new file:

1. Ensure `docs/` directory exists (create if not)
2. Check if `docs/SESSION_HANDOFF.md` exists
3. If yes, rename it to `SESSION_HANDOFF_old.md`
4. If `SESSION_HANDOFF_old.md` already exists, rename to `SESSION_HANDOFF_old2.md` (and so on)
5. Create the new `docs/SESSION_HANDOFF.md`

---

## Document Structure

Generate `SESSION_HANDOFF.md` with the following sections. **Omit any section that doesn't apply.**

### Section 1: Header with Instructions

```markdown
# Session Handoff — [Project Name]

> **How to use:** Paste this file into Claude Code after CLAUDE.md (auto-read) and BOOTSTRAP_PROMPT.md. This gives Claude the current project state.
>
> **Last updated:** [DATE]
```

---

### Section 2: Project Context (One-Liner)

Quick orientation — assumes reader has already read BOOTSTRAP_PROMPT.md.

```markdown
## Context

- **Project:** [One-line description]
- **Location:** `~/CODE/[path]`
- **Branch:** `master`
- **Runner:** `bun run` / `uv run python3`
```

---

### Section 3: Current Status

What's done and what's in progress. Use a table or bullet list.

**Table format (good for phased projects):**

```markdown
## Current Status

| Area | Status | Notes |
|------|--------|-------|
| Database schema | ✅ Complete | All migrations applied |
| Auth system | ✅ Complete | Better Auth configured |
| PDF upload | ✅ Complete | Working with validation |
| LLM extraction | 🔄 In Progress | Basic extraction works, schema matching incomplete |
| UI dashboard | ⏳ Not Started | Waiting on extraction |
| Export feature | ⏳ Not Started | Phase 2 |
```

**For pipeline/phased projects:**
- **One row per stage/step** — not collapsed into coarse phases. If the project has Steps 1–5.1, each gets its own row. This lets a new session immediately see which specific stage is in progress vs complete.
- **Include a `Test File(s)` column** mapping each stage to its test files. This lets the next session immediately find the relevant tests when working on a specific stage. Example: `filter-engine.test.ts, rule-management.test.ts`.
- **Include meta-work as rows** when significant — e.g., "Docs", "Infrastructure", "Tooling" — if they are active workstreams with their own status.

**Always include a test summary line** after the status table: total test count, file count, expect() count (if known), run command, approximate duration, and skip behavior. Example: "**Tests:** 512 pass across 24 files (799 `expect()` calls). Run: `just test` (~2 min). Tests skip gracefully when DB/LLM unavailable." This anchors the next session on test health without reading BOOTSTRAP.

**Bullet format (good for smaller projects):**

```markdown
## Current Status

**Done:**
- Project scaffolding and configuration
- Database schema and migrations
- Basic API endpoints
- Authentication flow

**In Progress:**
- LLM extraction pipeline (extraction works, schema matching needs work)

**Not Started:**
- Dashboard UI
- Export functionality
```

---

### Section 4: Implementation Status (If Phased)

For projects with defined phases or milestones. **Be as granular as the project's actual steps** — don't collapse 6 distinct stages into 2 vague phases. Each meaningful step should be its own row so a new session can see exactly where progress stalled or is active.

```markdown
## Implementation Phases

| Phase | Description | Status |
|-------|-------------|--------|
| Phase 1 | Core infrastructure | ✅ Complete |
| Phase 2 | PDF processing | ✅ Complete |
| Phase 3 | LLM integration | 🔄 In Progress (70%) |
| Phase 4 | Dashboard UI | ⏳ Not Started |
| Phase 5 | Export & Reports | ⏳ Not Started |

**Currently working on:** Phase 3 — specifically schema matching for extracted data.
```

For multi-step pipelines, prefer step-level granularity:

```markdown
| Phase | Status |
|-------|--------|
| Step 1: Download | ✅ Complete |
| Step 2: Ingest | ✅ Complete |
| Step 3: Filter (rules) | ✅ Complete (3 rounds, 60 rules) |
| Step 4: Classify (LLM) | 🔄 In Progress (FY2024-25 started) |
| Step 5: Signal extraction | ✅ Complete (24 types) |
| Docs: generator workflow | 🔄 In Progress |
```

---

### Section 5: Uncommitted Changes

What's been modified but not yet committed. Check with `git status`.

```markdown
## Uncommitted Changes

```bash
modified:   src/llm/extractor.ts      # Added retry logic
modified:   src/schemas/document.ts   # New field for confidence score
new file:   src/llm/schema-matcher.ts # WIP - schema matching logic
modified:   tests/llm/extractor.test.ts
```

**Summary:** Working on schema matching. Extractor changes are ready to commit; schema-matcher is WIP.
```

**Per-file annotations are mandatory.** Don't just show `git status` output — add a one-line description of what changed in each file. A new session needs to know *what* is in each file, not just that it's modified. Group files into "Ready to commit" vs "WIP" when applicable.

If nothing uncommitted:

```markdown
## Uncommitted Changes

Working tree clean. Last commit: `feat: add PDF upload validation`
```

---

### Section 6: What Comes Next

Concrete next steps — not a roadmap, but immediate actions.

**This section is SESSION_HANDOFF's most important contribution.** No other doc looks forward. BOOTSTRAP describes the system. EVOLUTION records past decisions. Only SESSION_HANDOFF answers "What should the next session work on, and what choices are open?"

**Strategic decisions MUST come first.** If the project has reached a fork — multiple viable paths forward, a decision that affects scope or architecture, or a choice that the next session needs to make before proceeding — present it as named options (Option A/B/C) with:
- Specific code references (DB columns, config paths, flags, table names)
- A "Key question" directive: a command or investigation that would inform the choice
- Enough context that a new session can make the call without re-reading the codebase

If there are no open strategic decisions, say so explicitly: "No open strategic decisions — proceed with immediate tasks."

Procedural tasks ("commit files", "run tests", "continue classification") go AFTER strategic context.

```markdown
## What Comes Next

**Open decision — resolve before proceeding:**

After classification completes, three paths forward:

- **Option A: LLM Enrichment** — Send `needs_llm_enrichment = true` emails for structured extraction. `email_signals` table already has the flag.
- **Option B: Human Review** — Build review workflow for `needs_review = true` classifications. DB columns exist: `reviewed`, `review_outcome` in `llm_classification_results`.
- **Option C: Skip to Export** — If signal coverage is sufficient, package data for accountant. Config paths wired: `packagesRoot`, `reportsRoot`.

**Key question:** Run `just extract-signals` then `just signals-report` to assess coverage. If strong signals cover most included emails, Option C is viable.

**Immediate (this session or next):**

1. **Finish schema matcher** — `src/llm/schema-matcher.ts` needs:
   - Field type inference from LLM output
   - Confidence scoring
   - Fallback for unmatched fields

2. **Test with real PDFs** — use samples in `test-data/invoices/`

3. **Commit extraction changes** — extractor.ts and tests are ready

**After that:**
- Wire up extraction to API endpoint
- Build minimal dashboard to view results
```

---

### Section 7: Critical Reminders

Things that aren't in CLAUDE.md or BOOTSTRAP but are essential right now. These are "don't forget" items.

```markdown
## Critical Reminders

- **LLM server must be running** — check http://192.168.1.56:1234 is accessible before testing extraction
- **Use `/no_think` flag** — schema matching prompts should use `/no_think` or you'll get reasoning in output
- **Test data is in `test-data/`** — don't use production PDFs for testing
- **Database was reset yesterday** — if you see missing data, that's why
```

If no critical reminders:

```markdown
## Critical Reminders

None currently. Standard workflow applies — see CLAUDE.md for commands.
```

---

### Section 8: Known Issues / Blockers

Current problems being worked around or blocking progress.

```markdown
## Known Issues

| Issue | Impact | Workaround |
|-------|--------|------------|
| LM Studio crashes on large PDFs | Can't process PDFs > 20 pages | Split PDFs manually first |
| Rate limiting not implemented | Could hit LLM server too fast | Add manual delays in testing |
| Schema matcher returns nulls | Low confidence extractions fail | Under investigation |

**Blockers:**
- None currently — development can proceed
```

If no issues:

```markdown
## Known Issues

No known issues currently blocking development.
```

**Include a "Resolved recently" subsection** when issues were fixed in the current or previous session. These prevent a new session from re-investigating solved problems. Format: issue name, fix description, commit hash (if available).

**Lifecycle:** Once a resolved issue is documented in `docs/EVOLUTION.md`, it can be dropped from SESSION_HANDOFF. EVOLUTION.md is the permanent record; SESSION_HANDOFF's "Resolved recently" is a temporary buffer (1-2 sessions) until the next EVOLUTION.md generation captures it.

---

### Section 9: Recent Decisions

Key choices made recently that affect current work. This section is a **staging area** — decisions live here temporarily (current + previous session) until the next EVOLUTION.md generation absorbs them.

**Only include decisions from the last 1-2 sessions.** If `docs/EVOLUTION.md` exists and already documents a decision, don't repeat it here — just reference EVOLUTION.md. This prevents the two files from drifting or contradicting each other.

```markdown
## Recent Decisions

| Decision | Why | Date |
|----------|-----|------|
| Use Zod for schema validation | Better TS integration than Yup, smaller bundle | 2026-04-01 |
| Qwen 3 over GPT-4 | Local inference, no API costs, good enough quality | 2026-03-28 |
| Store raw LLM responses | Debugging and reprocessing without re-calling LLM | 2026-03-28 |

See `docs/EVOLUTION.md` for the full decision history.
```

If no recent decisions:

```markdown
## Recent Decisions

No major decisions this session. See `docs/EVOLUTION.md` for the full decision history.
```

---

### Section 10: Session Notes (Optional)

Freeform notes from the current/recent session. Useful for complex debugging or multi-session work.

```markdown
## Session Notes

**2026-04-02 session:**
- Discovered schema matching fails when LLM returns nested objects
- Root cause: flatten logic assumes single-level JSON
- Fix approach: recursive flattening before matching
- Left off: wrote failing test, haven't implemented fix yet

**2026-04-01 session:**
- Basic extraction working end-to-end
- Tested with 5 sample invoices, 4/5 extracted correctly
- One failure was due to scanned PDF (image-only) — need vision model
```

---

## Quality Checklist

Before presenting the generated SESSION_HANDOFF.md, verify:

- [ ] **50-100 lines** — brief and focused
- [ ] **Status is accurate** — reflects actual git status and project state
- [ ] **Next steps are concrete** — specific files and tasks, not vague goals
- [ ] **No architecture content** — that belongs in BOOTSTRAP_PROMPT.md
- [ ] **No rules/commands** — those belong in CLAUDE.md
- [ ] **No duplicate decisions** — decisions already in EVOLUTION.md shouldn't be repeated here
- [ ] **Recent decisions documented** — only from last 1-2 sessions, with EVOLUTION.md cross-reference
- [ ] **Uncommitted changes listed** — from actual `git status`
- [ ] **Uncommitted changes annotated** — per-file descriptions, not just paths
- [ ] **Known issues are current** — remove resolved issues already in EVOLUTION.md
- [ ] **Resolved issues documented** — recently-fixed issues in "Resolved recently" (drop once in EVOLUTION.md)
- [ ] **Test file mapping included** — each pipeline stage linked to its test file(s)
- [ ] **Test summary line complete** — total count, file count, expect() count, run command, skip behavior
- [ ] **Phases are granular** — one row per step/stage, not collapsed into coarse groups
- [ ] **Meta-work tracked** — docs, tooling, infrastructure as status rows when active
- [ ] **Strategic decisions surfaced** — open choices presented as named options with code refs, DB columns, config paths
- [ ] **"What Comes Next" leads with strategy** — fork-in-the-road decisions before procedural tasks
- [ ] **No open forks buried in prose** — if there's a choice to make, it's a named Option A/B/C, not a sentence

---

## Examples

### Example 1: Early-Stage Project (~50 lines)

```markdown
# Session Handoff — url-shortener

> **How to use:** Paste after CLAUDE.md and BOOTSTRAP_PROMPT.md.
>
> **Last updated:** 2026-04-02

## Context

- **Project:** CLI tool for shortening URLs via is.gd
- **Location:** `~/CODE/tools/url-shortener`
- **Branch:** `master`
- **Runner:** `uv run python3`

## Current Status

**Done:**
- Project scaffolding (pyproject.toml, structure)
- Basic CLI with Click
- is.gd API client

**In Progress:**
- Clipboard integration (pyperclip)

**Not Started:**
- Error handling for rate limits
- Tests

## Uncommitted Changes

```bash
modified:   src/cli.py    # Added --copy flag
new file:   src/clipboard.py
```

## What Comes Next

1. **Finish clipboard integration** — test on macOS, handle missing pyperclip gracefully
2. **Add basic tests** — at least test the shortening logic
3. **Commit and tag v0.1.0**

## Critical Reminders

- is.gd has rate limits (10/min) — don't spam during testing

## Known Issues

None currently.

## Recent Decisions

| Decision | Why |
|----------|-----|
| pyperclip over pyobjc | Cross-platform, simpler API |
| Click over argparse | Better UX, less boilerplate |
```

---

### Example 2: Mid-Development Project (~80 lines)

```markdown
# Session Handoff — browser-automation

> **How to use:** Paste after CLAUDE.md and BOOTSTRAP_PROMPT.md.
>
> **Last updated:** 2026-04-02

## Context

- **Project:** Undetectable browser automation using macOS A11y API
- **Location:** `~/CODE/CaptainCodeAU/Browser_Automation_System`
- **Branch:** `master`
- **Runner:** `uv run python3`

## Current Status

| Component | Status | Notes |
|-----------|--------|-------|
| A11y controller | ✅ Complete | Can control Brave windows |
| Extension | ✅ Complete | DOM capture working |
| Native messaging | ✅ Complete | Bidirectional communication |
| DOM → Markdown | 🔄 In Progress | Basic conversion works, tables need work |
| CLI interface | ⏳ Not Started | Using Python REPL for now |

## Implementation Phases

| Phase | Status |
|-------|--------|
| 1. A11y research & POC | ✅ Complete |
| 2. Extension development | ✅ Complete |
| 3. Integration | ✅ Complete |
| 4. DOM conversion | 🔄 70% |
| 5. CLI & polish | ⏳ Not Started |

**Currently working on:** Phase 4 — DOM to Markdown conversion, specifically HTML tables.

## Uncommitted Changes

```bash
modified:   src/utils/dom_to_markdown.py   # Table handling WIP
new file:   tests/fixtures/table-heavy.html
modified:   tests/test_dom_to_markdown.py
```

## What Comes Next

1. **Fix table conversion** — current output loses column alignment
2. **Add test for nested tables** — edge case found on Wikipedia pages
3. **Commit DOM conversion improvements**
4. **Start CLI interface** — basic `capture <url>` command

## Critical Reminders

- **Brave must be running** — start manually before testing
- **Accessibility permission** — grant in System Preferences if first run
- **Extension must be installed** — `just extension-install` if missing

## Known Issues

| Issue | Workaround |
|-------|------------|
| Tables lose alignment | Manual cleanup for now |
| Very large DOMs timeout | Paginate or limit depth |

## Recent Decisions

| Decision | Why | Date |
|----------|-----|------|
| Brave only, not Chrome | Consistent testing, fewer variables | 2026-03-25 |
| Markdown over JSON output | More human-readable, easier to verify | 2026-03-26 |

## Session Notes

**2026-04-02:**
- Table conversion is harder than expected
- HTML tables often have colspan/rowspan, need to handle
- Consider using `tabulate` library for output formatting
```

---

### Example 3: Complex Multi-Service Project (~100 lines)

```markdown
# Session Handoff — tax-pipeline

> **How to use:** Paste after CLAUDE.md and BOOTSTRAP_PROMPT.md.
>
> **Last updated:** 2026-04-02

## Context

- **Project:** PDF extraction pipeline with local LLM inference
- **Location:** `~/CODE/CaptainCodeAU/Tax_Pipeline_1`
- **Branch:** `master`
- **Runner:** `bun run`

## Current Status

| Area | Status | Notes |
|------|--------|-------|
| Infrastructure | ✅ Complete | Docker, DB, LLM server |
| Auth | ✅ Complete | Better Auth + sessions |
| PDF upload | ✅ Complete | Validation, storage |
| PDF parsing | ✅ Complete | Text + image extraction |
| LLM extraction | 🔄 In Progress | Works but needs schema matching |
| Schema matching | 🔄 In Progress | Core logic, 60% done |
| Dashboard | ⏳ Not Started | Blocked on extraction |
| Export | ⏳ Not Started | Phase 2 |

## Implementation Phases

| Phase | Status |
|-------|--------|
| 1. Infrastructure | ✅ Complete |
| 2. Upload & Storage | ✅ Complete |
| 3. Extraction Pipeline | 🔄 75% |
| 4. Dashboard UI | ⏳ Blocked |
| 5. Export & Reports | ⏳ Not Started |

**Currently working on:** Phase 3 — schema matching to map LLM output to typed structures.

## Uncommitted Changes

```bash
modified:   src/llm/extractor.ts        # Retry logic, confidence scores
modified:   src/llm/prompts.ts          # Updated extraction prompt
new file:   src/llm/schema-matcher.ts   # WIP
modified:   src/schemas/extraction.ts   # Added confidence field
modified:   tests/llm/extractor.test.ts
new file:   tests/llm/schema-matcher.test.ts
```

**Ready to commit:** extractor.ts, prompts.ts, extraction.ts, extractor.test.ts
**WIP (don't commit yet):** schema-matcher.ts, schema-matcher.test.ts

## What Comes Next

1. **Finish schema matcher** (`src/llm/schema-matcher.ts`):
   - Implement recursive flattening for nested objects
   - Add type coercion (string dates → Date objects)
   - Handle arrays of items (line items on invoices)

2. **Test with more PDFs** — use `test-data/invoices/` samples

3. **Commit ready files** — extraction improvements are stable

4. **Wire to API** — `POST /api/extract` should use new schema matcher

## Critical Reminders

- **LLM server must be running** — `http://192.168.1.56:1234/v1/models` should respond
- **Use Qwen 3 text model** — vision model only for image-heavy PDFs
- **Always use `/no_think`** — extraction prompts must have this suffix
- **Database is local** — `just db-up` if Postgres not running

## Known Issues

| Issue | Impact | Workaround |
|-------|--------|------------|
| Nested object flattening fails | Can't extract line items | WIP — fixing now |
| Large PDFs slow | >20 pages takes 60s+ | Acceptable for now |
| Vision model not loaded | Image PDFs fail | Load manually in LM Studio |

**Blockers:** None — schema matcher is in progress but not blocking.

## Recent Decisions

| Decision | Why | Date |
|----------|-----|------|
| Store raw LLM JSON | Can reprocess without re-calling LLM | 2026-04-01 |
| Confidence threshold 0.7 | Below this, flag for human review | 2026-04-01 |
| Flatten before matching | Simpler matching logic | 2026-04-02 |

## Session Notes

**2026-04-02:**
- Schema matching is the crux — got basic fields working
- Problem: invoices have line items (arrays), current code assumes flat
- Solution: recursive flatten, keep array indices as keys like `items.0.description`
- Test case: `test-data/invoices/multi-item.pdf` has 5 line items

**2026-04-01:**
- Extraction working end-to-end for simple PDFs
- Prompt engineering made big difference — few-shot examples help
- `/no_think` is essential, otherwise Qwen reasons out loud
```

---

## After Generation

Once you've created `docs/SESSION_HANDOFF.md`, inform the user:

> **SESSION_HANDOFF.md created** ([X] lines).
>
> This file captures the current project state:
> - Status: [summary — e.g., "Phase 3 in progress, 75% complete"]
> - Uncommitted: [X] files modified
> - Strategic: [open decision or "no open forks"]
> - Next: [primary next task]
>
> Next: `"Read .prompts/EVOLUTION_GENERATOR.md and create docs/EVOLUTION.md"`
