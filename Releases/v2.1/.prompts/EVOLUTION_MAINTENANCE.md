# EVOLUTION_MAINTENANCE.md
<!-- Version: 2.1 -->

> **What this file does:** Appends new entries to `docs/EVOLUTION.md` to record decisions, features, and significant changes. This is how the project's institutional memory grows over time.
>
> **How to run:** Prompt Claude Code with: `"Read .prompts/EVOLUTION_MAINTENANCE.md and update docs/EVOLUTION.md"`
>
> **When to run:**
> - After making significant architectural decisions
> - After completing a feature or milestone
> - After fixing a significant bug (worth documenting the lesson)
> - After making breaking changes
> - When SESSION_HANDOFF.md has "Recent Decisions" that need to be recorded
> - Before major releases or version tags
>
> **Output:** Updates `docs/EVOLUTION.md` in place (renames existing to `EVOLUTION_old.md` first).

---

## Instructions for Claude Code

You are about to update `docs/EVOLUTION.md` by adding new entries for decisions and changes made during this session. This is an **append operation** — new entries go at the top (reverse chronological), existing entries are preserved.

**Process:**

1. Read the current `docs/EVOLUTION.md`
2. Identify what should be recorded from this session
3. Create new entries in the standard format
4. Insert new entries at the top (after the Summary section)
5. Update the Summary section if milestones changed
6. Rename existing file to `EVOLUTION_old.md` before writing

**Key principle:** EVOLUTION.md is the permanent record. Entries should be written as if explaining to a future developer (or future Claude) why things are the way they are.

---

## File Handling

Before updating:

1. Read current `docs/EVOLUTION.md` into memory
2. Rename it to `EVOLUTION_old.md` (or `_old2`, `_old3` if those exist)
3. Write the updated version as new `docs/EVOLUTION.md`

---

## Detection Phase

Gather entries to add from these sources:

### 1. Recent Decisions from SESSION_HANDOFF.md

Check `docs/SESSION_HANDOFF.md` for the "Recent Decisions" section:

```markdown
## Recent Decisions

| Decision | Why | Date |
|----------|-----|------|
| Flatten before match | Simpler logic | 2026-04-02 |
| Chunk large PDFs | Prevents crashes | 2026-04-03 |
```

**Action:** Convert each to a full EVOLUTION entry and remove from SESSION_HANDOFF.md

### 2. Git Commits Since Last Update

```bash
# Find significant commits
git log --oneline --since="last week"

# Look for decision/feature/fix indicators
git log --grep="decision\|chose\|instead of" -i --since="last week"
git log --grep="feat\|feature\|add\|implement" -i --since="last week"
git log --grep="fix\|bug\|resolve" -i --since="last week"
git log --grep="breaking\|migrate\|upgrade" -i --since="last week"
```

### 3. Session Context

From the current session:

- What significant decisions were made?
- What features were completed?
- What bugs were fixed that others should know about?
- What breaking changes were introduced?

### 4. Milestones

Check if any milestones were reached:

- Phase completed?
- Major feature shipped?
- Version tagged?

---

## Entry Format

Each entry follows this format:

```markdown
---

### [DATE] — [Type]: [Title]

**What:** [1-2 sentence description of what changed]

**Why:** [Explanation of the reasoning — this is the most important part]

**Details:**
- [Specific detail 1]
- [Specific detail 2]
- [Specific detail 3]

**Files:** `path/to/file.ts`, `path/to/other.ts`
```

### Entry Types

| Type | When to Use | Example Title |
|------|-------------|---------------|
| **Decision** | Chose A over B, architectural choice | "Decision: Use Redis for sessions" |
| **Feature** | New functionality added | "Feature: PDF export with templates" |
| **Bugfix** | Significant bug fixed (worth documenting) | "Bugfix: Memory leak in image processing" |
| **Breaking** | Changes that break existing behavior | "Breaking: API response format v2" |
| **Refactor** | Restructured without behavior change | "Refactor: Extract auth to separate module" |
| **Upgrade** | Dependency or platform upgrade | "Upgrade: Astro 4 → Astro 5" |
| **Deprecation** | Feature being phased out | "Deprecation: Legacy auth endpoints" |
| **Milestone** | Phase or version completed | "Milestone: Phase 2 complete" |

---

## Update Actions

### Adding New Entries

Insert new entries **after the Summary section** and **before existing entries**:

```markdown
# Evolution — project-name

> **What this is:** ...

## Summary

- **Current phase:** Phase 3 — LLM Integration
- **Started:** 2026-03-10
- **Major milestones:**
  - 2026-03-15 — Infrastructure complete
  - 2026-03-28 — PDF processing complete
  - 2026-04-03 — Schema matching complete ← ADD IF MILESTONE

---

### 2026-04-03 — Decision: Index arrays as dot notation ← NEW ENTRY

**What:** Array items in schema matching use `items.0.field` notation.

**Why:** Preserves structure while keeping flat key-value format. Easy to reconstruct original nested structure. Simpler matching logic than handling nested objects directly.

**Alternatives considered:**
- Keep nested structure — complicated matching logic
- Stringify arrays — loses ability to match individual items

**Files:** `src/llm/schema-matcher.ts`

---

### 2026-04-03 — Feature: PDF chunking for large documents ← NEW ENTRY

**What:** PDFs over 20 pages are now split into chunks for processing.

**Why:** LM Studio crashes with OOM on large PDFs. Chunking keeps memory usage stable and allows progress reporting.

**Details:**
- Chunk size: 10 pages
- Chunks processed sequentially
- Results merged after all chunks complete
- Added progress callback for UI

**Files:** `src/pdf/chunker.ts`, `src/llm/extractor.ts`

---

### 2026-04-02 — Decision: Flatten before schema matching ← EXISTING ENTRY

...existing entries continue...
```

### Updating Summary Section

If a milestone was reached:

```markdown
## Summary

- **Current phase:** Phase 4 — Dashboard UI ← UPDATED
- **Started:** 2026-03-10
- **Major milestones:**
  - 2026-03-15 — Infrastructure complete
  - 2026-03-28 — PDF processing complete
  - 2026-04-03 — Schema matching complete ← ADDED
```

### Updating Decision Index (If Present)

If the file has a Key Decisions Index:

```markdown
## Key Decisions Index

| ID | Decision | Date | Status |
|----|----------|------|--------|
| D-001 | Local LLM over cloud API | 2026-03-12 | Active |
| D-002 | Drizzle over Prisma | 2026-03-10 | Active |
| D-006 | Index arrays as dot notation | 2026-04-03 | Active | ← ADD
| D-007 | Chunk large PDFs | 2026-04-03 | Active | ← ADD
```

Increment the ID number for each new decision.

---

## Writing Good Entries

### The "Why" is Most Important

Bad:
```markdown
**Why:** It was better.
```

Good:
```markdown
**Why:** Local LLM inference eliminates per-token API costs, keeps sensitive tax documents on-premise, and removes rate limit concerns. Tradeoff is lower capability than GPT-4, but sufficient for structured extraction tasks.
```

### Include Alternatives Considered (for Decisions)

```markdown
**Alternatives considered:**
- Prisma — More popular but heavier, slower cold starts
- Raw SQL — Maximum control but no type safety
- Kysely — Good middle ground but less ecosystem support
```

### Include Root Cause (for Bugfixes)

```markdown
**What:** Fixed memory leak causing crashes after processing ~50 PDFs.

**Why:** Image buffers weren't being released after extraction.

**Root cause:** Sharp library holds references until garbage collection, but Node's GC wasn't running frequently enough under load.

**Fix:** Explicitly call `sharp.cache(false)` and null out buffer references after use.
```

### Be Specific About Breaking Changes

```markdown
**What:** API response format changed from `{ data, error }` to `{ success, data?, error? }`.

**Why:** Explicit success boolean makes error checking cleaner. Aligns with our other APIs.

**Migration:**
- All clients must check `success` field
- `data` is now optional (undefined on error)
- `error` is now optional (undefined on success)

**Affected endpoints:** All `/api/*` routes
```

---

## Suggestion Phase

After updating EVOLUTION.md, consider whether other files need updates:

### Suggest SESSION_MAINTENANCE if:

- Moved decisions from SESSION_HANDOFF.md to here
- Need to clear the "Recent Decisions" section

**Output:**
> 💡 **Suggestion:** Decisions have been recorded in EVOLUTION.md. Run SESSION_MAINTENANCE to clear the "Recent Decisions" section in SESSION_HANDOFF.md.

### Suggest BOOTSTRAP_MAINTENANCE if:

- A bugfix entry reveals a quirk that should be documented
- An architecture decision affects module boundaries

**Output:**
> 💡 **Suggestion:** The bugfix for Sharp memory leak should be added to BOOTSTRAP_PROMPT.md as a quirk with the workaround.

### Suggest CLAUDE_MAINTENANCE if:

- A decision changes rules or conventions
- Breaking changes affect how developers should work

**Output:**
> 💡 **Suggestion:** The API response format change should be noted in CLAUDE.md under Patterns.

---

## Quality Checklist

Before presenting the updated EVOLUTION.md, verify:

- [ ] **New entries at top** — reverse chronological order maintained
- [ ] **"Why" is substantive** — not just "it was better"
- [ ] **Dates are accurate** — when the decision/change actually happened
- [ ] **Types are correct** — using standard vocabulary
- [ ] **Files are listed** — key files affected
- [ ] **Summary updated** — if milestone reached
- [ ] **Decision Index updated** — if present and decisions added
- [ ] **No duplicate entries** — check existing entries first
- [ ] **Old file backed up** — EVOLUTION_old.md exists

---

## Examples

### Example 1: Recording Decisions from Session

**Source:** SESSION_HANDOFF.md has two recent decisions.

**New entries to add:**

```markdown
---

### 2026-04-03 — Decision: Chunk large PDFs

**What:** PDFs over 20 pages are split into 10-page chunks for LLM processing.

**Why:** LM Studio runs out of memory processing large documents in one pass. Chunking keeps memory stable (~4GB vs 12GB+) and allows progress feedback to users.

**Details:**
- Threshold: 20 pages
- Chunk size: 10 pages
- Processing: Sequential (parallel caused race conditions)
- Results: Merged with page number tracking

**Alternatives considered:**
- Increase server RAM — expensive, doesn't scale
- Stream processing — LM Studio doesn't support streaming input
- Reduce context window — loses too much document context

**Files:** `src/pdf/chunker.ts`, `src/llm/extractor.ts`

---

### 2026-04-03 — Decision: Index arrays with dot notation

**What:** Nested arrays in schema matching use `items.0.field` key format.

**Why:** Maintains flat key-value structure for simpler matching while preserving array semantics. Easy to reconstruct nested structure when needed.

**Details:**
- Array items become: `items.0.description`, `items.1.description`
- Nested objects: `address.street`, `address.city`
- Reconstruction function provided: `unflatten()`

**Files:** `src/llm/schema-matcher.ts`, `src/utils/flatten.ts`
```

---

### Example 2: Recording Completed Feature

**Source:** Completed the export module this session.

**New entry:**

```markdown
---

### 2026-04-03 — Feature: PDF and CSV export

**What:** Users can now export extraction results as PDF reports or CSV spreadsheets.

**Why:** Core requirement for tax workflow — accountants need formatted reports and data in Excel-compatible format.

**Details:**
- PDF: Uses pdfkit, custom templates in `src/export/templates/`
- CSV: Uses papaparse, handles nested data via flattening
- Both respect user's timezone for date formatting
- Batch export available for multiple documents

**Files:** `src/export/index.ts`, `src/export/pdf.ts`, `src/export/csv.ts`, `src/export/templates/`
```

**Also update Summary:**

```markdown
## Summary

- **Current phase:** Phase 4 — Dashboard UI
- **Major milestones:**
  - ...
  - 2026-04-03 — Export functionality complete ← ADD
```

---

### Example 3: Recording Significant Bugfix

**Source:** Fixed a memory leak that was causing production issues.

**New entry:**

```markdown
---

### 2026-04-03 — Bugfix: Memory leak in image extraction

**What:** Fixed memory leak causing server crashes after processing ~50 PDFs.

**Why:** Image buffers from Sharp weren't being released, accumulating until OOM.

**Root cause:** Sharp caches processed images by default. Under high load, cache grew unbounded. Node's GC wasn't aggressive enough to reclaim memory before OOM.

**Fix:**
- Disabled Sharp cache: `sharp.cache(false)`
- Explicitly null buffer references after use
- Added memory monitoring in dev mode

**Lesson learned:** Always check library caching behavior when processing many files.

**Files:** `src/pdf/images.ts:45-67`
```

---

### Example 4: Recording Breaking Change

**Source:** Changed API response format.

**New entry:**

```markdown
---

### 2026-04-03 — Breaking: API response format v2

**What:** All API endpoints now return `{ success: boolean, data?: T, error?: string }` instead of `{ data, error }`.

**Why:** Explicit success boolean eliminates ambiguity. Previous format required checking both `data` and `error` to determine success. New format: just check `success`.

**Migration required:**
```typescript
// Before
if (response.data) { ... }

// After  
if (response.success) { ... }
```

**Affected:** All `/api/*` endpoints

**Files:** `src/api/*.ts`, `src/types/api.ts`
```

---

### Example 5: No New Entries Needed

**Detected:** No significant decisions, features, or bugs this session.

**Output:**
> **No EVOLUTION.md updates needed.**
>
> This session involved:
> - Minor bug fixes (not significant enough to document)
> - Work in progress (not yet complete)
> - No architectural decisions
>
> EVOLUTION.md remains current. Consider running EVOLUTION_MAINTENANCE when:
> - A feature is completed
> - A significant decision is made
> - A notable bug is fixed with lessons learned

---

## After Update

Once you've updated `docs/EVOLUTION.md`, inform the user:

> **EVOLUTION.md updated** ([X] total entries, [+Y] new).
>
> **New entries added:**
> - [Date] — [Type]: [Title]
> - [Date] — [Type]: [Title]
>
> **Summary updated:** [Yes, milestone added / No changes]
>
> **Backup:** Previous version saved as `EVOLUTION_old.md`
>
> [Include any suggestions for other maintenance prompts]
>
> **Institutional memory preserved.** Future sessions will understand why these decisions were made.
