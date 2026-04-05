# BOOTSTRAP_MAINTENANCE.md

<!-- Version: 2.2 -->

> **What this file does:** Updates `docs/BOOTSTRAP_PROMPT.md` when architecture, module boundaries, or quirks have changed. Run this before commits when you've made structural changes to the project.
>
> **How to run:** Prompt Claude Code with: `"Read .prompts/BOOTSTRAP_MAINTENANCE.md and update docs/BOOTSTRAP_PROMPT.md"`
>
> **When to run:**
>
> - Added or restructured modules
> - Discovered and fixed bugs (new quirks to document)
> - Changed data flow or architecture
> - Subsystem isolation boundaries changed (new tables, new ownership)
> - Added important new files that should be in the reading list
> - Changed exports in existing key files (new public functions, renamed types)
> - Changed infrastructure (new machines, services, ports)
> - Updated testing setup significantly
> - Added or changed shared type files (types.ts, interfaces.ts)
>
> **Output:** Updates `docs/BOOTSTRAP_PROMPT.md` in place (renames existing to `BOOTSTRAP_PROMPT_old.md` first).

---

## Instructions for Claude Code

You are about to update `docs/BOOTSTRAP_PROMPT.md` based on changes made during this session. This is a **maintenance task**, not a regeneration — preserve existing content and add/modify only what's changed.

**Process:**

1. Read the current `docs/BOOTSTRAP_PROMPT.md`
2. Detect what has changed in the project
3. Update the relevant sections
4. Preserve everything that hasn't changed
5. Rename existing file to `BOOTSTRAP_PROMPT_old.md` before writing

**Key principles:**

> **Cardinal rule:** If removing a line from BOOTSTRAP_PROMPT.md would force the next session to open a source file to understand the architecture, keep the line. BOOTSTRAP exists to prevent source-file reads during onboarding.

> **Anti-degradation:** Never remove or simplify existing content during maintenance unless you have confirmed the underlying source has changed. Only add or modify. See Anti-Degradation Safeguards below.

> **Right-sizing:** BOOTSTRAP_PROMPT.md is sized to project complexity tier (Simple: 80-130, Medium: 130-175, Complex: 175-250 lines — see BOOTSTRAP_GENERATOR.md for the Content Allocation Guide). If an update would push it past the tier's hard maximum, flag to the user rather than silently cutting content.

---

## File Handling

Before updating:

1. Read current `docs/BOOTSTRAP_PROMPT.md` into memory
2. Rename it to `BOOTSTRAP_PROMPT_old.md` (or `_old2`, `_old3` if those exist)
3. Write the updated version as new `docs/BOOTSTRAP_PROMPT.md`

---

## Detection Phase

Scan for changes in these areas:

### 1. Module Boundaries Changed

Check for new or restructured modules:

```bash
# List top-level source directories
ls -la src/

# Find barrel files (module entry points)
find src -maxdepth 2 -name "index.ts" -o -name "index.js" -o -name "__init__.py" -o -name "mod.rs"

# Check for new directories since last commit
git diff --name-only --diff-filter=A HEAD~10 | grep "src/" | cut -d'/' -f2 | sort -u
```

**Signs of boundary changes:**

- New folder in `src/` with exports
- Module renamed or merged
- Responsibilities shifted between modules
- New wrapper module around external dependency

**Action if found:** Update the "Module Boundaries" table (3 columns: Module | Owns | Key Exports)

### 2. New Quirks Discovered

Look for bugs that were debugged and fixed this session:

**Sources to check:**

- Git commits with "fix", "bug", "workaround" in message
- Code comments added: `NOTE:`, `HACK:`, `FIXME:`, `BUG:`
- Session context — what was debugged?
- Error messages that led to non-obvious solutions
- Defensive code patterns: sanitisation functions, encoding guards, format converters that exist because the raw/unguarded path fails. If someone bypassing the function would hit a bug, it's a documentable quirk.

**For each quirk, gather:**

- File and line number (if applicable)
- What the symptom was
- What the root cause was
- How to avoid or handle it
- Commit hash of the fix (`git log --oneline --grep="keyword"`)

**Action if found:** Add to the "Quirks & Gotchas" section with file:line references, root cause, and commit hash. If total quirks reach 10+, ensure they are categorized by subsystem.

### 3. Architecture Changed

Check for changes to data flow or system structure:

**Signs of architecture changes:**

- New service added (Docker, external API)
- Data flow path changed
- New integration point
- Significant refactoring of core logic
- New pipeline stage added or existing stage's scope changed
- Subsystem gained or lost ownership of tables/data
- Subsystem isolation boundaries shifted (e.g., classification now touches filter data)

**Action if found:**

- Update the "Architecture" section (key patterns and data flow)
- Update subsystem isolation descriptions if ownership changed (tables owned, data NOT touched, reversal mechanism)
- Update infrastructure diagram if multi-machine setup changed

### 4. Key Files Changed

Check if important files were added or significantly changed:

```bash
# New files in key locations
git diff --name-only --diff-filter=A HEAD~10

# Significantly modified files (by line count)
git diff --stat HEAD~10 | head -20
```

**Files that should be in reading list:**

- Entry points
- Schema definitions
- Core business logic
- Configuration files
- New module entry points (index.ts, **init**.py)
- **Shared type definition files** (`types.ts`, `interfaces.ts`) — these define the vocabulary every module uses. If new, they must be in the mandatory reading list.

**Signs of key export changes in EXISTING files:**

- Function renamed or removed from a module's public API
- New important export added (new public function, new type, new class)
- Module's barrel file (index.ts) changed its re-exports
- Shared type file gained new foundational types used across modules

**Action if found:** Update the "Key Files" table (4 columns: File | Purpose | Size | Key Exports), ensuring every entry has 2-5 exports listed by name. If a shared type file changed, also update the reading list annotation. Update "Reading List" if new mandatory files were added.

### 5. Infrastructure Changed

For multi-machine setups:

```bash
# Check Docker changes
git diff docker-compose.yml

# Check environment changes
git diff .env.example

# Check for new service configurations
```

**Signs of infrastructure changes:**

- New container added
- Ports changed
- New machine in the network
- New environment variables required

**Action if found:** Update the infrastructure diagram and any related notes

### 6. Testing Setup Changed

Check for testing changes:

```bash
# Test file count
find tests -name "*.test.*" -o -name "*.spec.*" -o -name "test_*.py" | wc -l

# New test configuration
git diff vitest.config.* jest.config.* pytest.ini pyproject.toml
```

**Action if found:** Update the "Testing" section with new counts, patterns, or configuration

### 8. Module Coverage Gap

Audit whether all library modules appear in the Key Files table:

```bash
# List all .ts files in src/lib/ (or equivalent)
ls src/lib/*.ts

# Compare against Key Files entries in current BOOTSTRAP_PROMPT.md
```

**Signs of coverage gap:**

- File exists in `src/lib/` with exported functions but is not in Key Files
- This can happen when BOOTSTRAP was generated before the module existed, or when the generator missed it

**Action if found:** Add missing module to Key Files table with all 4 columns populated (File | Purpose | Size | Key Exports). If the module has a public API used by other code, it belongs in the table regardless of size.

### 9. Platform Gotchas Changed

Check if new platform-specific issues were discovered:

- macOS-specific behavior
- Browser extension limitations (MV3)
- Framework quirks (Astro, Next.js, etc.)
- Runtime differences (Node vs Bun)

**Action if found:** Update the "Platform Gotchas" section

---

## Update Actions

For each detected change, update the appropriate section using the correct format:

### Adding to Module Boundaries Table

Use 3 columns: Module | Owns | Key Exports.

```markdown
## Module Boundaries

| Module        | Owns                          | Key Exports                                                  |
| ------------- | ----------------------------- | ------------------------------------------------------------ | ---------- |
| `src/db/`     | Database access, all DB types | `getDb()`, `closeDb()`, row interfaces, `computeDedupHash()` |
| `src/llm/`    | LLM communication             | `chatJSON()`, `chatText()`, `ensureServerReady()`            |
| `src/export/` | File generation (PDF, CSV)    | `toPDF()`, `toCSV()`, `ExportConfig`                         | ← ADD THIS |
```

### Adding a New Quirk

Add with file:line reference, root cause, and commit hash:

```markdown
## Quirks & Gotchas

1. **Existing quirk** (`file.ts:123`)
    - Description...

2. **Existing quirk** (`other.ts:45`)
    - Description...

3. **Sharp fails silently on corrupt images** (`src/pdf/images.ts:67`) ← ADD THIS
    - Image extraction returns empty array, no error thrown
    - Root cause: Sharp catches errors internally for some formats
    - Fix: Wrap in try/catch and check output dimensions. Commit: `abc1234`
```

**Quirk format rules:**

- Include root cause when known (not just the symptom)
- Include commit hash for the fix when available (`git log --oneline --grep="keyword"`)
- If total quirks reach 10+, categorize by subsystem (e.g., LLM, Database, Pipeline, Platform)
- **Never drop existing quirks** when adding new ones — verify the bug was un-fixed before removing

### Updating Key Files Table

Use 4 columns: File | Purpose | Size | Key Exports.

```markdown
## Key Files

| File                | Purpose         | Size      | Key Exports                    |
| ------------------- | --------------- | --------- | ------------------------------ | ---------- |
| `src/index.ts`      | App entry point | 2KB       | `startServer()`, `app`         |
| `src/db/schema.ts`  | Database schema | 649 lines | All table definitions, 2 views |
| `src/export/pdf.ts` | PDF generation  | 8KB       | `generatePDF()`, `PDFConfig`   | ← ADD THIS |
```

**Format rules:**

- **Key Exports column is mandatory** — list 2-5 exports by name for every entry
- Size column helps signal file complexity (lines or KB)
- Never add a file entry without populating Key Exports — "Database queries" is insufficient; list the actual exported functions
- List subsystem files individually, never collapse to "signals/ — 7 files"

### Updating Reading List

```markdown
## Reading List

### Mandatory

1. `CLAUDE.md` — Rules and commands
2. `src/index.ts` — Entry point
   ...
3. `src/export/index.ts` — Export module entry point ← ADD THIS

### Optional

- `src/export/templates/` — PDF templates (if working on exports) ← ADD THIS
```

**Note:** Shared type files (`types.ts`, `interfaces.ts`) belong in the **Mandatory** section, not Optional — they define the vocabulary every module uses.

### Updating Architecture — Subsystem Isolation

When a subsystem's ownership changes, update the subsystem isolation block:

```markdown
**Subsystem isolation:**

- **Ingestion** — writes to `records`, `attachments`, `ingest_runs`. Never touches filter or classification data. Reversible via rollback command.
- **Export** — writes to `exports` table and output directory. Never touches source records. ← ADD THIS
```

Each entry must name: tables/data owned, what it does NOT touch, and reversal mechanism.

### Updating Infrastructure Diagram

If multi-machine setup changed:

```markdown
## Infrastructure
```

┌─────────────────────────────────────────────────────────────┐
│ Mac Mini M4 (Dev) │
│ ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐ │
│ │ Astro App │ │ Docker │ │ New Service │ │ ← UPDATED
│ │ :4321 │ │ Postgres │ │ :9000 │ │ ← UPDATED
│ └─────────────┘ │ :5432 │ └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘

```

```

### Updating Testing Section

```markdown
## Testing

- **Framework:** bun:test
- **Location:** `tests/` mirrors `src/` structure
- **Run all:** `just test`

**Current count:** ~52 tests, ~82% coverage ← UPDATE THESE NUMBERS

**New patterns:**

- Export tests use temp directories, cleaned up after ← ADD IF NEW
```

---

## Suggestion Phase

After updating BOOTSTRAP_PROMPT.md, consider whether other files need updates:

### Suggest CLAUDE_MAINTENANCE if:

- New module boundary needs a corresponding rule
- Quirk should also be a gotcha in CLAUDE.md (short version)
- Commands changed as part of this work

**Output:**

> 💡 **Suggestion:** New module `src/export/` was added. Consider running CLAUDE_MAINTENANCE to add a boundary rule preventing direct file writes outside this module.

### Suggest EVOLUTION_MAINTENANCE if:

- Architecture change represents a significant decision
- New module was a deliberate choice over alternatives
- Bug fix reveals something worth documenting for history

**Output:**

> 💡 **Suggestion:** Adding the export module was a significant architectural decision. Consider running EVOLUTION_MAINTENANCE to document why this was split out rather than kept in the API layer.

### Suggest SESSION_MAINTENANCE if:

- These changes affect current project status
- New work is now possible that was blocked before

**Output:**

> 💡 **Suggestion:** Export module is now complete. Consider running SESSION_MAINTENANCE to update the project status.

---

## Anti-Degradation Safeguards

These rules prevent maintenance from eroding the BOOTSTRAP_PROMPT.md quality:

1. **Never remove key exports from existing file entries.** If a file already has `Key Exports: getDb(), closeDb(), computeDedupHash()`, do not shorten this to "Database functions" or remove the column. The export names are API-reference quality content.

2. **Never collapse individually-listed subsystem files.** If the bootstrap lists 7 signal pattern files individually, do not consolidate to "signals/ — 7 pattern files". Each file has distinct exports that callers need.

3. **Never remove quirks that still exist in the code.** Before removing any quirk, verify the bug was un-fixed (reverted) or the file was deleted. Quirks are the most valuable section — err on the side of keeping.

4. **Preserve subsystem isolation descriptions.** If Architecture has "Ingestion writes to records, attachments, ingest_runs. Never touches filter or classification data." — do not summarize or remove this. These descriptions prevent cross-subsystem mistakes.

5. **Preserve table column structure.** Key Files must keep all 4 columns (File | Purpose | Size | Key Exports). Module Boundaries must keep all 3 columns (Module | Owns | Key Exports). Do not drop columns to "simplify."

6. **Update stats, don't remove them.** If it says "512 tests", update to the current count. Don't delete the stat.

7. **If the file exceeds the hard maximum after updates, flag to the user.** Do not silently cut content. Say: "BOOTSTRAP_PROMPT.md is now [X] lines, which exceeds the [tier] hard max of [Y]. Would you like to trim the [section] section?"

8. **Maintain complete library module coverage.** Every file in `src/lib/` (or equivalent) with public exports must appear in Key Files. During maintenance, cross-check the directory listing against the Key Files table. Missing modules are a coverage regression, not an intentional omission.

9. **Never restructure sections during maintenance.** If the section structure needs reworking, that's a regeneration task (use BOOTSTRAP_GENERATOR.md). Maintenance only adds, modifies, and preserves.

---

## Cross-Reference Guide — What Goes Where

When new content is detected during maintenance, decide where it belongs:

| Content Type                  | CLAUDE.md | BOOTSTRAP                                   |
| ----------------------------- | --------- | ------------------------------------------- |
| Full command reference        | YES       | Quick ref (8-12 cmds)                       |
| Module boundary (1-line rule) | YES       | Detailed table with exports                 |
| Gotcha with fix (1-line)      | YES       | Detailed with file:line, root cause, commit |
| Architecture explanation      | —         | YES (primary home)                          |
| Key file exports              | —         | YES (API reference)                         |
| Shared type definitions       | —         | YES (in reading list)                       |
| Subsystem isolation details   | —         | YES (in architecture)                       |
| Complete quirks catalogue     | —         | YES (ALL bugs)                              |
| Env var reference table       | YES       | —                                           |
| Model IDs / service details   | YES       | —                                           |

**Litmus test:** If it helps understand _how the system works_, it belongs in BOOTSTRAP. If it's needed to _execute a command correctly_, it belongs in CLAUDE.md.

---

## Quality Checklist

Before presenting the updated BOOTSTRAP_PROMPT.md, verify:

**Anti-degradation**

- [ ] No existing key exports were removed from file entries
- [ ] No individually-listed subsystem files were collapsed
- [ ] All existing quirks still present (none accidentally dropped)
- [ ] Subsystem isolation descriptions preserved in Architecture
- [ ] Table column structures preserved (Key Files: 4 cols, Module Boundaries: 3 cols)
- [ ] Stats (test counts, file counts) updated, not removed

**Completeness**

- [ ] Every key file entry has 2-5 exports listed by name in Key Exports column
- [ ] Shared type files (`types.ts`, `interfaces.ts`) present in reading list
- [ ] All quirks have file:line references where applicable
- [ ] New quirks include commit hashes where available
- [ ] Quirks categorized by subsystem if 10+ total
- [ ] Module boundaries table lists all modules
- [ ] All library modules (`src/lib/*.ts` or equivalent) with public exports are in Key Files table

**Right-sizing**

- [ ] Within target range for project complexity tier (Simple 80-130, Medium 130-175, Complex 175-250)
- [ ] If over hard max after updates, flagged to user (not silently cut)

**Accuracy**

- [ ] Key files actually exist — verify paths
- [ ] Reading list is ordered — foundational files first
- [ ] No duplicate content from CLAUDE.md — rules and full command dumps stay there

**Format**

- [ ] Old file backed up — BOOTSTRAP_PROMPT_old.md exists
- [ ] Formatting consistent with existing sections

---

## Examples

### Example 1: Adding New Module

**Detected:** New `src/export/` module created with PDF and CSV export.

**Updates needed:**

1. **Module Boundaries Table:**

```markdown
| `src/export/` | File generation (PDF, CSV) | `toPDF()`, `toCSV()`, `ExportConfig` |
```

2. **Key Files Table:**

```markdown
| `src/export/index.ts` | Export module entry | 1KB | `toPDF()`, `toCSV()` |
| `src/export/pdf.ts` | PDF generation | 8KB | `generatePDF()`, `PDFConfig` |
| `src/export/csv.ts` | CSV generation | 3KB | `generateCSV()`, `CSVConfig` |
```

3. **Reading List (Optional section):**

```markdown
- `src/export/templates/` — PDF templates (if working on export features)
```

**Suggestions:**

> 💡 **Suggestion:** Consider running CLAUDE_MAINTENANCE to add a boundary rule for `src/export/`.
>
> 💡 **Suggestion:** Consider running EVOLUTION_MAINTENANCE to document the decision to use pdfkit over alternatives.

---

### Example 2: Adding New Quirk

**Detected:** During debugging, discovered that `better-auth` session cookies don't work in HTMX requests without explicit credentials.

**Update to Quirks section:**

```markdown
8. **Better Auth sessions fail in HTMX** (`src/api/auth.ts:34`)
    - HTMX requests return 401 even when logged in
    - Root cause: HTMX doesn't send credentials by default
    - Fix: Add `hx-headers='{"credentials": "include"}'` to HTMX elements, or configure `hx-config` globally. Commit: `e7f8a9b`
```

**Suggestion:**

> 💡 **Suggestion:** This is a common gotcha. Consider running CLAUDE_MAINTENANCE to add a short version to the Gotchas section.

---

### Example 3: Infrastructure Change

**Detected:** Added Redis for session storage, running in Docker.

**Updates needed:**

1. **Infrastructure Diagram:**

```markdown

```

┌─────────────────────────────────────────────────────────────┐
│ Mac Mini M4 (Dev) │
│ ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐ │
│ │ Astro App │ │ Docker │ │ Docker │ │
│ │ :4321 │ │ Postgres │ │ Redis │ │
│ └─────────────┘ │ :5432 │ │ :6379 │ │
│ └─────────────┘ └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘

```

```

2. **Key Files:**

```markdown
| `src/auth/session-store.ts` | Redis session storage | 4KB | `SessionStore`, `getSession()`, `destroySession()` |
```

3. **Reading List:**

```markdown
7. `docker-compose.yml` — Docker services (Postgres, Redis)
```

**Suggestion:**

> 💡 **Suggestion:** Consider running EVOLUTION_MAINTENANCE to document why Redis was chosen for sessions over database-backed sessions.

---

### Example 4: Updating Test Count

**Detected:** Test count increased from 45 to 67 after adding export module tests.

**Update:**

```markdown
## Testing

- **Framework:** bun:test
- **Location:** `tests/` mirrors `src/` structure
- **Run all:** `just test`

**Current count:** ~67 tests, ~85% coverage on core modules

**Patterns:**

- Export tests use `tmp/` directory, cleaned up in `afterEach`
- PDF tests use snapshot comparison
```

---

### Example 5: No Changes Detected

**Detected:** No architectural changes this session.

**Output:**

> **No BOOTSTRAP_PROMPT.md updates needed.**
>
> I checked for changes to:
>
> - Module boundaries — no new modules
> - Quirks — no new bugs discovered and fixed
> - Architecture — no structural changes, subsystem isolation unchanged
> - Key files — no significant additions or export changes
> - Key exports — existing file exports unchanged
> - Shared type files — no new types added to reading list files
> - Infrastructure — unchanged
> - Testing — count unchanged
> - Reading list — still current
>
> BOOTSTRAP_PROMPT.md remains current. No backup created since no changes were made.

---

## After Update

Once you've updated `docs/BOOTSTRAP_PROMPT.md`, inform the user:

> **BOOTSTRAP_PROMPT.md updated** ([X] lines, [+Y/-Z] net change, complexity tier: [simple/medium/complex]).
>
> **Changes made:**
>
> - [List each change, e.g., "Added `src/export/` to module boundaries with key exports"]
> - [List each change, e.g., "Added quirk #8: Better Auth HTMX sessions (commit: e7f8a9b)"]
> - [List each change, e.g., "Updated test count: 45 → 67"]
> - [List each change, e.g., "Updated key exports for `src/lib/db.ts`: added `migrateSchema()`"]
>
> **Anti-degradation check:** [Confirmed no content removed / Removed N stale entries (listed with verification)]
>
> **Backup:** Previous version saved as `BOOTSTRAP_PROMPT_old.md`
>
> [Include any suggestions for other maintenance prompts]
