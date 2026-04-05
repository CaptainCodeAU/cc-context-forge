# SESSION_MAINTENANCE.md
<!-- Version: 2.2 -->

> **What this file does:** Updates `docs/SESSION_HANDOFF.md` to reflect current project state. Run this at the end of a session or before commits to ensure the next session starts with accurate context.
>
> **How to run:** Prompt Claude Code with: `"Read .prompts/SESSION_MAINTENANCE.md and update docs/SESSION_HANDOFF.md"`
>
> **When to run:**
> - End of a coding session (before exiting/clearing)
> - Before committing code (to capture current state)
> - After completing a significant task or milestone
> - When blockers are discovered or resolved
> - When switching focus to a different area
>
> **Output:** Updates `docs/SESSION_HANDOFF.md` in place (renames existing to `SESSION_HANDOFF_old.md` first).
>
> **Companion docs:** SESSION_HANDOFF.md is the sole *forward-looking* doc. EVOLUTION.md owns backward-looking history; BOOTSTRAP_PROMPT.md owns architecture. During maintenance, respect these boundaries — don't let backward-looking content (decided and done) accumulate in SESSION_HANDOFF when it belongs in EVOLUTION.md.

---

## Instructions for Claude Code

You are about to update `docs/SESSION_HANDOFF.md` based on work done during this session. This is the most frequently updated file — it should always reflect the **current state** of the project.

**Process:**

1. Read the current `docs/SESSION_HANDOFF.md`
2. Assess what changed during this session
3. Update all relevant sections
4. Remove stale information
5. Rename existing file to `SESSION_HANDOFF_old.md` before writing

**Key principle:** SESSION_HANDOFF.md is a snapshot of "right now" that looks forward. Three docs, three questions:
- **BOOTSTRAP_PROMPT.md** → "How does this work?" (architecture, stable)
- **SESSION_HANDOFF.md** → "Where are we now and what's next?" (volatile state + forward-looking choices)
- **EVOLUTION.md** → "How did we get here?" (backward-looking decisions)

SESSION_HANDOFF is the only doc that looks *forward* — its most important contribution is surfacing open strategic choices and upcoming forks. Protect this during maintenance: don't let procedural updates displace strategic context.

---

## File Handling

Before updating:

1. Read current `docs/SESSION_HANDOFF.md` into memory
2. Rename it to `SESSION_HANDOFF_old.md` (or `_old2`, `_old3` if those exist)
3. Write the updated version as new `docs/SESSION_HANDOFF.md`

---

## Detection Phase

Gather current state from these sources:

### 1. Git Status

```bash
# What's modified but not committed?
git status --short

# What's been committed since last session?
git log --oneline -10
```

**Capture:**
- Modified files (with brief description of changes)
- New files (what they're for)
- Files ready to commit vs still WIP
- **Annotate each file** — don't just list paths; describe what changed in each (one line). This is the most-read section of the handoff.

### 2. Task Progress

Assess what was accomplished this session:

- What tasks were completed?
- What tasks are in progress?
- What tasks are blocked?
- What new tasks were discovered?

**Compare against:** Previous SESSION_HANDOFF.md status section

### 3. Implementation Phases (If Applicable)

For phased projects:

- Did any phase status change?
- What percentage complete is the current phase?
- Is it time to move to the next phase?
- **Maintain one row per step/stage** — don't collapse granular steps into coarse phases during updates. If the project has Steps 1–5.1, each stays its own row.
- **Include meta-work rows** (Docs, Infrastructure, Tooling) when they're active workstreams.

### 4. Blockers and Issues

- Were any blockers resolved?
- Were new blockers discovered?
- Are there workarounds in use?

### 5. Decisions Made

- Were any significant decisions made this session?
- These should be noted here AND flagged for EVOLUTION.md

### 6. Test Changes

- Did the test count change? (Run `just test` or check test output)
- Were new test files added?
- Are any tests failing?
- Update the test summary line in the status section: total count, file count, `expect()` count (if known), run command, approximate duration, and skip behavior. Example: "**Tests:** 512 pass across 24 files (799 `expect()` calls). Run: `just test` (~2 min). Tests skip gracefully when DB/LLM unavailable."

### 7. Critical Context

- Any session-specific knowledge that must not be lost?
- Debugging insights?
- Things that "almost worked" and why?

---

## Update Actions

Update each section based on detected changes:

### Updating Current Status

**Table format (for phased projects):**

```markdown
## Current Status

| Area | Status | Notes |
|------|--------|-------|
| Database schema | ✅ Complete | All migrations applied |
| Auth system | ✅ Complete | Better Auth configured |
| PDF upload | ✅ Complete | ← WAS 🔄, NOW ✅ |
| LLM extraction | 🔄 In Progress | Schema matching 80% done ← UPDATED |
| UI dashboard | ⏳ Not Started | Blocked on extraction |
```

**Preserve the test file mapping column.** If the status table has a `Test File(s)` column, keep it current. If new test files were added for a stage, add them. If a stage's tests were refactored, update the file names.

**Bullet format (for simpler projects):**

```markdown
## Current Status

**Done:**
- Project scaffolding ✓
- Database schema ✓
- PDF upload with validation ✓ ← MOVED FROM IN PROGRESS

**In Progress:**
- LLM schema matching (80% complete) ← UPDATED PERCENTAGE

**Not Started:**
- Dashboard UI
- Export functionality
```

### Updating Uncommitted Changes

Get fresh status from git:

```markdown
## Uncommitted Changes

```bash
modified:   src/llm/schema-matcher.ts   # Completed recursive flattening
modified:   src/llm/extractor.ts        # Added confidence scores
new file:   tests/llm/schema-matcher.test.ts
deleted:    src/llm/old-matcher.ts      # Removed deprecated code
```

**Ready to commit:**
- `src/llm/extractor.ts` — stable, tested
- `tests/llm/schema-matcher.test.ts` — passing

**WIP (don't commit yet):**
- `src/llm/schema-matcher.ts` — array handling incomplete
```

If clean:

```markdown
## Uncommitted Changes

Working tree clean. Last commit: `feat: complete schema matching`
```

### Updating What Comes Next

Be specific and actionable:

```markdown
## What Comes Next

**Immediate (next session):**

1. **Finish array handling in schema matcher**
   - File: `src/llm/schema-matcher.ts`
   - Remaining: handle nested arrays (e.g., invoice line items)
   - Test case: `test-data/invoices/multi-item.pdf`

2. **Commit completed work**
   - extractor.ts and tests are ready
   - Schema matcher after array fix

**After that:**
- Wire extraction to API endpoint (`POST /api/extract`)
- Build minimal results viewer
```

**Strategic options are the highest-priority content in this section.** "What Comes Next" is SESSION_HANDOFF's most important contribution — no other doc looks forward.

- If the previous SESSION_HANDOFF had open decisions (Option A/B/C), **carry them forward** unless they've been resolved this session.
- If a decision was made this session, move it to "Recent Decisions" and update "What Comes Next" to reflect the chosen path.
- If new forks emerged during this session, **add them as named options** (Option A/B/C) with specific code references (DB columns, config paths, flags).
- If there are no open strategic decisions, say so explicitly: "No open strategic decisions — proceed with immediate tasks."
- **Never let procedural housekeeping displace strategic context.** Procedural tasks go after strategy.

### Updating Critical Reminders

Add or remove based on current relevance:

```markdown
## Critical Reminders

- **LLM server IP changed** — now at 192.168.1.57 (was .56) ← NEW
- **Use `/no_think` flag** — still essential for extraction prompts
- ~~Test data is in `test-data/`~~ ← REMOVE IF NO LONGER RELEVANT
- **Schema matcher expects flat input** — flatten before calling ← NEW
```

### Updating Known Issues

Add resolved, remove fixed:

```markdown
## Known Issues

| Issue | Impact | Status |
|-------|--------|--------|
| ~~LM Studio crashes on large PDFs~~ | ~~High~~ | ✅ Fixed — chunking implemented |
| Nested arrays not handled | Medium | 🔄 In Progress |
| Rate limiting not implemented | Low | ⏳ Backlog |

**Blockers:** None currently
```

### Updating Resolved Issues

When issues are fixed this session:

1. Move them from "Known Issues" to a "Resolved recently" subsection
2. Add the fix description and commit hash
3. **Lifecycle:** Once a resolved issue is documented in `docs/EVOLUTION.md`, drop it from SESSION_HANDOFF. EVOLUTION.md is the permanent record; SESSION_HANDOFF's "Resolved recently" is a temporary buffer (1-2 sessions) until the next EVOLUTION maintenance captures it.

```markdown
**Resolved recently:**

| Issue | Fix | Commit |
|-------|-----|--------|
| ~~Nested arrays skipped~~ | Recursive flattening in schema-matcher.ts | `a1b2c3d` |
```

### Updating Recent Decisions

This section is a **staging area** — decisions live here temporarily (current + previous session) until EVOLUTION_MAINTENANCE absorbs them.

**Only include decisions from the last 1-2 sessions.** If `docs/EVOLUTION.md` already documents a decision, don't repeat it — replace with an EVOLUTION.md cross-reference.

```markdown
## Recent Decisions

| Decision | Why | Date |
|----------|-----|------|
| Chunk large PDFs | Prevents LM Studio crashes | 2026-04-03 | ← NEW

See `docs/EVOLUTION.md` for the full decision history.
```

### Updating Session Notes

Add notes from this session, archive or remove old ones:

```markdown
## Session Notes

**2026-04-03 (this session):** ← ADD NEW
- Completed recursive flattening for schema matcher
- Discovered issue with nested arrays — invoice line items fail
- Workaround: skip nested arrays for now, handle in next session
- LLM server moved to .57 — updated .env

**2026-04-02:**
- Basic schema matching working
- Tested with 5 invoices, 4/5 pass
```

Keep only last 2-3 sessions of notes. Older notes should be in EVOLUTION.md.

---

## Suggestion Phase

After updating SESSION_HANDOFF.md, consider whether other files need updates:

### Suggest EVOLUTION_MAINTENANCE if:

- Significant decisions were made (in "Recent Decisions" section)
- Features were completed
- Major bugs were fixed

**Output:**
> 💡 **Suggestion:** You made 2 decisions this session (flatten before match, chunk large PDFs). Consider running EVOLUTION_MAINTENANCE to add these to the permanent decision log.

### Suggest BOOTSTRAP_MAINTENANCE if:

- New quirks were discovered during debugging
- Architecture changed
- New module boundaries established

**Output:**
> 💡 **Suggestion:** You discovered a quirk about nested arrays in schema matching. Consider running BOOTSTRAP_MAINTENANCE to add this to the Quirks section.

### Suggest CLAUDE_MAINTENANCE if:

- Commands changed
- New rules established
- Gotchas discovered that should be in quick reference

**Output:**
> 💡 **Suggestion:** The LLM server IP changed. Consider running CLAUDE_MAINTENANCE to update the Machine Context section.

---

## Quality Checklist

Before presenting the updated SESSION_HANDOFF.md, verify:

- [ ] **Status is accurate** — reflects actual git status
- [ ] **Completed items moved** — from "In Progress" to "Done"
- [ ] **Next steps are concrete** — specific files and tasks
- [ ] **Stale info removed** — old reminders, resolved issues already in EVOLUTION.md
- [ ] **No duplicate decisions** — decisions already in EVOLUTION.md replaced with cross-reference
- [ ] **Recent decisions as staging** — only last 1-2 sessions, with EVOLUTION.md cross-reference
- [ ] **Session notes are current** — this session added, old pruned
- [ ] **Uncommitted changes match reality** — run `git status` to verify
- [ ] **Uncommitted changes annotated** — descriptions, not just paths
- [ ] **Test file mapping preserved** — column still accurate
- [ ] **Test summary line complete** — total count, file count, expect() count, run command, skip behavior
- [ ] **Phases are granular** — one row per step/stage, not collapsed during updates
- [ ] **Meta-work tracked** — docs, tooling, infrastructure as status rows when active
- [ ] **Strategic options carried forward** — unless decision was made this session
- [ ] **"What Comes Next" leads with strategy** — forks before procedures
- [ ] **No open forks buried in prose** — choices are named Option A/B/C with code refs
- [ ] **Resolved issues lifecycle** — drop from SESSION_HANDOFF once in EVOLUTION.md
- [ ] **50-100 lines** — brief and focused

---

## Examples

### Example 1: End of Productive Session

**Session summary:** Completed schema matching feature, discovered array handling issue.

**Updates:**

```markdown
# Session Handoff — tax-pipeline

> **Last updated:** 2026-04-03

## Context

- **Project:** PDF extraction pipeline with local LLM
- **Location:** `~/CODE/CaptainCodeAU/Tax_Pipeline_1`
- **Branch:** `master`
- **Runner:** `bun run`

## Current Status

| Area | Status | Notes |
|------|--------|-------|
| PDF upload | ✅ Complete | |
| PDF parsing | ✅ Complete | |
| LLM extraction | ✅ Complete | Basic extraction working |
| Schema matching | 🔄 In Progress | 90% — arrays remaining |
| Dashboard | ⏳ Not Started | |

## Uncommitted Changes

```bash
modified:   src/llm/schema-matcher.ts   # Recursive flattening done
modified:   src/llm/extractor.ts        # Confidence scores added
new file:   tests/llm/schema-matcher.test.ts
```

**Ready to commit:** extractor.ts, tests
**WIP:** schema-matcher.ts (array handling)

## What Comes Next

1. **Handle nested arrays** in `src/llm/schema-matcher.ts:145`
   - Invoice line items come as arrays
   - Current code skips them — need to iterate

2. **Commit stable changes** — extractor is ready

3. **Test with multi-item invoices** — `test-data/invoices/multi-item.pdf`

## Critical Reminders

- LLM server at 192.168.1.57 (changed from .56)
- Use `/no_think` for extraction prompts
- Schema matcher expects flat input — flatten first

## Known Issues

| Issue | Status |
|-------|--------|
| Nested arrays skipped | 🔄 Fixing |

## Recent Decisions

| Decision | Why | Date |
|----------|-----|------|
| Chunk large PDFs | Prevents OOM | 2026-04-03 |

## Session Notes

**2026-04-03:**
- Recursive flattening complete and tested
- Found array issue when testing multi-item invoice
- Server IP changed — updated .env
```

---

### Example 2: Session with Blocker

**Session summary:** Hit a blocker, documented workaround, pivoted to other work.

**Updates:**

```markdown
## Current Status

| Area | Status | Notes |
|------|--------|-------|
| Schema matching | 🚫 Blocked | Vision model won't load |
| PDF text extraction | ✅ Complete | Pivoted to this |

## Known Issues

| Issue | Impact | Workaround |
|-------|--------|------------|
| Vision model OOM | High — can't process image PDFs | Use text-only model, skip image PDFs |

**Blockers:**
- Vision model (qwen2.5-vl) causes OOM on RTX 3090
- Need to investigate memory settings in LM Studio
- For now: skip image-heavy PDFs, process text-only

## What Comes Next

1. **Investigate LM Studio memory settings** — may need to reduce context
2. **Or: implement PDF splitting** — process pages one at a time
3. **Meanwhile:** continue with text-based PDFs

## Session Notes

**2026-04-03:**
- Vision model crashes with OOM on any image PDF
- Spent 1hr debugging, no solution yet
- Pivoted to improving text extraction instead
- Text extraction now handles 95% of our PDFs anyway
```

---

### Example 3: Clean Commit Ready

**Session summary:** All work complete, ready to commit and close.

**Updates:**

```markdown
## Current Status

| Area | Status | Notes |
|------|--------|-------|
| Schema matching | ✅ Complete | All tests passing |
| Array handling | ✅ Complete | Invoice line items work |

## Uncommitted Changes

```bash
modified:   src/llm/schema-matcher.ts
modified:   tests/llm/schema-matcher.test.ts
```

All changes tested and ready to commit.

## What Comes Next

1. **Commit this work:** `git commit -m "feat: complete schema matching with array support"`
2. **Start dashboard UI** — can now display extraction results
3. **Run maintenance prompts** — EVOLUTION needs decision log update

## Critical Reminders

None — standard workflow applies.

## Known Issues

None currently blocking.

## Recent Decisions

| Decision | Why | Date |
|----------|-----|------|
| Index arrays as `items.0.field` | Preserves structure, easy to reconstruct | 2026-04-03 |

→ **Run EVOLUTION_MAINTENANCE** to record this decision.
```

---

### Example 4: Minimal Update

**Session summary:** Small bug fix, minimal state change.

**Updates:**

Only update what changed:

```markdown
## Uncommitted Changes

```bash
modified:   src/utils/date-parser.ts   # Fixed timezone handling
modified:   tests/utils/date-parser.test.ts
```

Ready to commit: `fix: handle UTC offset in date parsing`

## Known Issues

| Issue | Status |
|-------|--------|
| ~~Dates wrong in exports~~ | ✅ Fixed |
```

Everything else stays the same.

---

## After Update

Once you've updated `docs/SESSION_HANDOFF.md`, inform the user:

> **SESSION_HANDOFF.md updated** ([X] lines).
>
> **State captured:**
> - Status: [summary, e.g., "Schema matching 90% complete"]
> - Uncommitted: [X] files ([Y] ready to commit, [Z] WIP)
> - Next: [primary next task]
> - Blockers: [none / description]
>
> **Backup:** Previous version saved as `SESSION_HANDOFF_old.md`
>
> [Include any suggestions for other maintenance prompts]
>
> **Ready to commit/exit.** Next session: paste BOOTSTRAP → EVOLUTION → this file after CLAUDE.md loads.
