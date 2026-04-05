# CLAUDE_MAINTENANCE.md
<!-- Version: 1.0 -->

> **What this file does:** Updates `CLAUDE.md` when rules, commands, or conventions have changed. Run this before commits when you've modified the project's tooling, boundaries, or patterns.
>
> **How to run:** Prompt Claude Code with: `"Read .prompts/CLAUDE_MAINTENANCE.md and update CLAUDE.md"`
>
> **When to run:**
> - Added or changed commands (Justfile, package.json scripts, Makefile)
> - Added new module boundaries or rules
> - Discovered new gotchas that bit you
> - Changed conventions (branch, runner, commit style)
> - Updated tech stack (new dependencies, removed packages)
>
> **Output:** Updates `CLAUDE.md` in place (renames existing to `CLAUDE_old.md` first).

---

## Instructions for Claude Code

You are about to update `CLAUDE.md` based on changes made during this session. This is a **maintenance task**, not a regeneration — preserve existing content and add/modify only what's changed.

**Process:**

1. Read the current `CLAUDE.md`
2. Detect what has changed in the project
3. Update the relevant sections
4. Preserve everything that hasn't changed
5. Rename existing file to `CLAUDE_old.md` before writing

**Key principle:** CLAUDE.md stays lean (<200 lines). If an update would push it over, consider whether the content belongs in BOOTSTRAP_PROMPT.md instead.

---

## File Handling

Before updating:

1. Read current `CLAUDE.md` into memory
2. Rename it to `CLAUDE_old.md` (or `_old2`, `_old3` if those exist)
3. Write the updated version as new `CLAUDE.md`

This ensures you can diff changes and rollback if needed.

---

## Detection Phase

Scan for changes in these areas:

### 1. Commands Changed

Check these files for new or modified commands:

| File | What to Look For |
|------|------------------|
| `Justfile` | New recipes, changed recipe names, new categories |
| `Makefile` | New targets |
| `package.json` | New or changed `scripts` entries |
| `pyproject.toml` | New `[tool.poetry.scripts]` or `[project.scripts]` |
| `deno.json` | New `tasks` |

**Compare against:** Current "Commands" section in CLAUDE.md

**Action if changed:** Update the Commands section, maintain categorization if >10 commands

### 2. New Module Boundaries

Check for new directories or significant restructuring:

```bash
# New directories in src/
ls -la src/

# New barrel files (indicate module boundaries)
find src -name "index.ts" -o -name "index.js" -o -name "__init__.py"
```

**Signs of new boundaries:**
- New folder in `src/` with its own `index.*` file
- New wrapper module around an SDK or library
- Code moved from one module to another

**Action if found:** Add to the "Boundaries" section under Rules

### 3. New Gotchas Discovered

Look for issues discovered during this session:

- Bugs that were debugged and fixed
- Unexpected behavior that required workarounds
- Things that "should work but don't"
- Platform-specific issues encountered

**Sources:**
- Recent git commits with "fix", "workaround", "bug"
- Comments added with NOTE, HACK, FIXME
- Conversation context (what was debugged this session)

**Action if found:** Add to the "Gotchas" section under Rules

### 4. Tech Stack Changed

Check for dependency changes:

```bash
# Node/Bun projects
git diff package.json

# Python projects
git diff pyproject.toml requirements.txt

# Check for new major dependencies
```

**Action if changed:** Update the Tech Stack table

### 5. Conventions Changed

Check if any conventions have changed:

- Default branch (still `master`?)
- Runner command (still `bun run`? `uv run python3`?)
- Commit style
- PR workflow (or lack thereof)

**Action if changed:** Update the Conventions section

### 6. Machine Context Changed

For multi-machine setups:

- New machines added?
- Ports changed?
- New services running?
- LLM models changed?

**Action if changed:** Update the Machine Context section

---

## Update Actions

For each detected change, update the appropriate section:

### Adding a New Command

```markdown
## Commands

### [Existing Category]
```bash
existing-command   # Existing description
new-command        # New description ← ADD THIS
```
```

If adding many commands, consider whether a new category is needed.

### Adding a New Boundary Rule

```markdown
### Boundaries — Never Cross These

1. [Existing boundary]
2. [Existing boundary]
3. **`src/newmodule/`** owns [responsibility] — never [violation] ← ADD THIS
```

### Adding a New Gotcha

```markdown
### Gotchas — Things That Have Bitten Us

1. [Existing gotcha]
2. [Existing gotcha]
3. **[New issue]** — [description of what goes wrong and how to avoid it] ← ADD THIS
```

### Updating Tech Stack

Add or modify rows in the table:

```markdown
## Tech Stack

| Layer | Technology |
|-------|------------|
| ... | ... |
| [New Layer] | [New Technology] | ← ADD THIS
```

### Updating Machine Context

```markdown
## Machine Context

| Machine | Role | Address |
|---------|------|---------|
| ... | ... | ... |
| [New Machine] | [Role] | [Address] | ← ADD THIS
```

---

## Suggestion Phase

After updating CLAUDE.md, consider whether other files need updates:

### Suggest BOOTSTRAP_MAINTENANCE if:

- New module boundaries were added (needs detailed explanation)
- New quirks were discovered (needs file:line references)
- Architecture changed significantly
- New key files were added

**Output:**
> 💡 **Suggestion:** New module boundaries were added. Consider running BOOTSTRAP_MAINTENANCE to update the module boundaries table and add detailed documentation.

### Suggest EVOLUTION_MAINTENANCE if:

- Major decisions were made this session
- Significant features were completed
- Breaking changes were introduced

**Output:**
> 💡 **Suggestion:** A significant decision was made (chose X over Y). Consider running EVOLUTION_MAINTENANCE to document this in the decision log.

### Suggest SESSION_MAINTENANCE if:

- Project state has changed significantly
- Tasks were completed or started
- New blockers discovered

**Output:**
> 💡 **Suggestion:** Project state has changed. Consider running SESSION_MAINTENANCE to update the current status.

---

## Quality Checklist

Before presenting the updated CLAUDE.md, verify:

- [ ] **Still under 200 lines** — move content to BOOTSTRAP if too long
- [ ] **No duplicate content added** — check if it already exists
- [ ] **New commands are accurate** — test them if unsure
- [ ] **Gotchas are specific** — not vague warnings
- [ ] **Formatting is consistent** — matches existing style
- [ ] **Old file backed up** — CLAUDE_old.md exists

---

## Examples

### Example 1: Adding New Commands

**Detected:** New `just db-seed` command added to Justfile.

**Before:**
```markdown
### Database
```bash
just db-up        # Start PostgreSQL container
just db-down      # Stop PostgreSQL container
just db-migrate   # Run pending migrations
```
```

**After:**
```markdown
### Database
```bash
just db-up        # Start PostgreSQL container
just db-down      # Stop PostgreSQL container
just db-migrate   # Run pending migrations
just db-seed      # Seed database with test data
```
```

---

### Example 2: Adding New Gotcha

**Detected:** During this session, discovered that Drizzle Studio crashes if database is empty.

**Before:**
```markdown
### Gotchas — Things That Have Bitten Us

1. **Qwen 3 thinks by default** — append `/no_think` to prompts for simple extractions
2. **First LLM request is slow** — JIT model loading takes 10-30s
```

**After:**
```markdown
### Gotchas — Things That Have Bitten Us

1. **Qwen 3 thinks by default** — append `/no_think` to prompts for simple extractions
2. **First LLM request is slow** — JIT model loading takes 10-30s
3. **Drizzle Studio crashes on empty DB** — run `just db-seed` first or it fails silently
```

---

### Example 3: Adding New Module Boundary

**Detected:** New `src/export/` module created for PDF/CSV export functionality.

**Before:**
```markdown
### Boundaries — Never Cross These

1. **`src/db/`** owns all database access — never import `drizzle` elsewhere
2. **`src/llm/`** owns all LLM calls — never call the LLM API directly from routes
3. **`src/auth/`** owns authentication — never check sessions outside this module
```

**After:**
```markdown
### Boundaries — Never Cross These

1. **`src/db/`** owns all database access — never import `drizzle` elsewhere
2. **`src/llm/`** owns all LLM calls — never call the LLM API directly from routes
3. **`src/auth/`** owns authentication — never check sessions outside this module
4. **`src/export/`** owns all file generation — never create PDFs/CSVs elsewhere
```

**Also suggest:**
> 💡 **Suggestion:** New module `src/export/` was added. Consider running BOOTSTRAP_MAINTENANCE to add it to the module boundaries table with full details.

---

### Example 4: Updating Tech Stack

**Detected:** Added `sharp` for image processing.

**Before:**
```markdown
## Tech Stack

| Layer | Technology |
|-------|------------|
| Runtime | Bun 1.x |
| Framework | Astro 5 (SSR) |
| Database | PostgreSQL 16 |
| ORM | Drizzle |
```

**After:**
```markdown
## Tech Stack

| Layer | Technology |
|-------|------------|
| Runtime | Bun 1.x |
| Framework | Astro 5 (SSR) |
| Database | PostgreSQL 16 |
| ORM | Drizzle |
| Image Processing | Sharp |
```

---

### Example 5: No Changes Detected

**Detected:** No changes to commands, boundaries, gotchas, or tech stack.

**Output:**
> **No CLAUDE.md updates needed.**
>
> I checked for changes to:
> - Commands (Justfile, package.json) — no changes
> - Module boundaries — no new modules
> - Gotchas — no new issues discovered
> - Tech stack — no dependency changes
> - Conventions — unchanged
>
> CLAUDE.md remains current. No backup created since no changes were made.

---

## After Update

Once you've updated `CLAUDE.md`, inform the user:

> **CLAUDE.md updated** ([X] lines, [+Y/-Z] net change).
>
> **Changes made:**
> - [List each change, e.g., "Added `just db-seed` command"]
> - [List each change, e.g., "Added gotcha about Drizzle Studio"]
>
> **Backup:** Previous version saved as `CLAUDE_old.md`
>
> [Include any suggestions for other maintenance prompts]
