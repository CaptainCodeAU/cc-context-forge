# BOOTSTRAP_MAINTENANCE.md
<!-- Version: 2.0 -->

> **What this file does:** Updates `docs/BOOTSTRAP_PROMPT.md` when architecture, module boundaries, or quirks have changed. Run this before commits when you've made structural changes to the project.
>
> **How to run:** Prompt Claude Code with: `"Read .prompts/BOOTSTRAP_MAINTENANCE.md and update docs/BOOTSTRAP_PROMPT.md"`
>
> **When to run:**
> - Added or restructured modules
> - Discovered and fixed bugs (new quirks to document)
> - Changed data flow or architecture
> - Added important new files that should be in the reading list
> - Changed infrastructure (new machines, services, ports)
> - Updated testing setup significantly
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

**Key principle:** BOOTSTRAP_PROMPT.md is for deep understanding (100-200 lines). It explains architecture and documents quirks in detail. Rules and commands stay in CLAUDE.md.

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

**Action if found:** Update the "Module Boundaries" table

### 2. New Quirks Discovered

Look for bugs that were debugged and fixed this session:

**Sources to check:**
- Git commits with "fix", "bug", "workaround" in message
- Code comments added: `NOTE:`, `HACK:`, `FIXME:`, `BUG:`
- Session context — what was debugged?
- Error messages that led to non-obvious solutions

**For each quirk, gather:**
- File and line number (if applicable)
- What the symptom was
- What the root cause was
- How to avoid or handle it

**Action if found:** Add to the "Quirks & Gotchas" section with file:line references

### 3. Architecture Changed

Check for changes to data flow or system structure:

**Signs of architecture changes:**
- New service added (Docker, external API)
- Data flow path changed
- New integration point
- Significant refactoring of core logic

**Action if found:** 
- Update the "Architecture" section
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
- New module entry points (index.ts, __init__.py)

**Action if found:** Update the "Key Files" table and "Reading List"

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

### 7. Platform Gotchas Changed

Check if new platform-specific issues were discovered:

- macOS-specific behavior
- Browser extension limitations (MV3)
- Framework quirks (Astro, Next.js, etc.)
- Runtime differences (Node vs Bun)

**Action if found:** Update the "Platform Gotchas" section

---

## Update Actions

For each detected change, update the appropriate section:

### Adding to Module Boundaries Table

```markdown
## Module Boundaries

| Module | Owns | Exports | Never Import Here |
|--------|------|---------|-------------------|
| `src/db/` | Database access | Query functions | Raw SQL |
| `src/llm/` | LLM communication | `extractData()` | Direct API calls |
| `src/export/` | File generation | `toPDF()`, `toCSV()` | Direct file writes | ← ADD THIS
```

### Adding a New Quirk

Add with file:line reference and full context:

```markdown
## Quirks & Gotchas

1. **Existing quirk** (`file.ts:123`)
   - Description...

2. **Existing quirk** (`other.ts:45`)
   - Description...

3. **Sharp fails silently on corrupt images** (`src/pdf/images.ts:67`) ← ADD THIS
   - Symptom: Image extraction returns empty array, no error thrown
   - Cause: Sharp catches errors internally for some formats
   - Fix: Wrap in try/catch and check output dimensions
   - Added: 2026-04-03
```

### Updating Key Files Table

```markdown
## Key Files

| File | Purpose | Key Exports / Notes |
|------|---------|---------------------|
| `src/index.ts` | App entry point | Starts server |
| `src/db/schema.ts` | Database schema | Table definitions |
| `src/export/pdf.ts` | PDF generation | `generatePDF()` | ← ADD THIS
```

### Updating Reading List

```markdown
## Reading List

### Mandatory
1. `CLAUDE.md` — Rules and commands
2. `src/index.ts` — Entry point
...
8. `src/export/index.ts` — Export module entry point ← ADD THIS

### Optional
- `src/export/templates/` — PDF templates (if working on exports) ← ADD THIS
```

### Updating Infrastructure Diagram

If multi-machine setup changed:

```markdown
## Infrastructure

```
┌─────────────────────────────────────────────────────────────┐
│                     Mac Mini M4 (Dev)                       │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │ Astro App   │  │  Docker     │  │  New Service        │ │ ← UPDATED
│  │ :4321       │  │  Postgres   │  │  :9000              │ │ ← UPDATED
│  └─────────────┘  │  :5432      │  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```
```

### Updating Testing Section

```markdown
## Testing

- **Framework:** bun:test
- **Location:** `tests/` mirrors `src/` structure
- **Run all:** `just test`
- **Watch mode:** `just test-watch`

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

## Quality Checklist

Before presenting the updated BOOTSTRAP_PROMPT.md, verify:

- [ ] **Still 100-200 lines** — not bloated
- [ ] **Quirks have file:line references** — specific, not vague
- [ ] **Module boundaries table is complete** — all modules listed
- [ ] **Key files actually exist** — verify paths
- [ ] **Reading list is ordered** — foundational files first
- [ ] **No duplicate content from CLAUDE.md** — rules stay there
- [ ] **Old file backed up** — BOOTSTRAP_PROMPT_old.md exists

---

## Examples

### Example 1: Adding New Module

**Detected:** New `src/export/` module created with PDF and CSV export.

**Updates needed:**

1. **Module Boundaries Table:**
```markdown
| `src/export/` | File generation (PDF, CSV) | `toPDF()`, `toCSV()`, `ExportConfig` | Direct file system writes |
```

2. **Key Files Table:**
```markdown
| `src/export/index.ts` | Export module entry | `toPDF()`, `toCSV()` |
| `src/export/pdf.ts` | PDF generation | Uses pdfkit |
| `src/export/csv.ts` | CSV generation | Uses papaparse |
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
   - Symptom: HTMX requests return 401 even when logged in
   - Cause: HTMX doesn't send credentials by default
   - Fix: Add `hx-headers='{"credentials": "include"}'` to HTMX elements
   - Or: Configure `hx-config` globally with credentials
   - Added: 2026-04-03
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
│                     Mac Mini M4 (Dev)                       │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │ Astro App   │  │  Docker     │  │  Docker             │ │
│  │ :4321       │  │  Postgres   │  │  Redis              │ │
│  └─────────────┘  │  :5432      │  │  :6379              │ │
│                   └─────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```
```

2. **Key Files:**
```markdown
| `src/auth/session-store.ts` | Redis session storage | `SessionStore` class |
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
> - Module boundaries — no new modules
> - Quirks — no new bugs discovered and fixed
> - Architecture — no structural changes
> - Key files — no significant additions
> - Infrastructure — unchanged
> - Testing — count unchanged
>
> BOOTSTRAP_PROMPT.md remains current. No backup created since no changes were made.

---

## After Update

Once you've updated `docs/BOOTSTRAP_PROMPT.md`, inform the user:

> **BOOTSTRAP_PROMPT.md updated** ([X] lines, [+Y/-Z] net change).
>
> **Changes made:**
> - [List each change, e.g., "Added `src/export/` to module boundaries"]
> - [List each change, e.g., "Added quirk #8: Better Auth HTMX sessions"]
> - [List each change, e.g., "Updated test count: 45 → 67"]
>
> **Backup:** Previous version saved as `BOOTSTRAP_PROMPT_old.md`
>
> [Include any suggestions for other maintenance prompts]
