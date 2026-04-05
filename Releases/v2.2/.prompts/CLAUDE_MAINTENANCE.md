# CLAUDE_MAINTENANCE.md
<!-- Version: 2.2 -->

> **What this file does:** Updates `CLAUDE.md` when rules, commands, conventions, or infrastructure have changed. Run this before commits when you've modified the project's tooling, boundaries, patterns, or services.
>
> **How to run:** Prompt Claude Code with: `"Read .prompts/CLAUDE_MAINTENANCE.md and update CLAUDE.md"`
>
> **When to run:**
> - Added or changed commands (Justfile, package.json scripts, Makefile)
> - Added new module boundaries or rules
> - Discovered new gotchas that bit you
> - Changed conventions (branch, runner, commit style)
> - Updated tech stack (new dependencies, removed packages)
> - Added or changed env vars used with task runner commands
> - Changed infrastructure (new machines, models, ports, services)
> - Added new domain conventions (date formats, currency, naming patterns)
> - Changed project structure (new directories, moved files)
>
> **Output:** Updates `CLAUDE.md` in place (renames existing to `CLAUDE_old.md` first).

---

## Instructions for Claude Code

You are about to update `CLAUDE.md` based on changes made during this session. This is a **maintenance task**, not a regeneration — preserve existing content and add/modify only what's changed.

**Process:**

1. Read the current `CLAUDE.md`
2. Detect what has changed in the project (see Detection Phase below)
3. Update the relevant sections
4. Preserve everything that hasn't changed
5. Rename existing file to `CLAUDE_old.md` before writing

**Key principles:**

> **Cardinal rule:** A new Claude Code session must be able to construct any project command, reference any env var, and know every module boundary from CLAUDE.md alone. If a maintenance update would remove information needed for this, keep the information.

> **Anti-degradation:** Never remove existing content during maintenance unless you have confirmed it's stale by checking the source (Justfile, package.json, src/ directory, .env.example). Only add or modify. See Anti-Degradation Safeguards below.

> **Right-sizing:** CLAUDE.md is right-sized to project complexity (Simple: 40-80, Medium: 80-150, Complex: 150-250 lines — see CLAUDE_GENERATOR.md for the Content Allocation Guide). If an update would push it past the tier's hard maximum, flag to the user rather than silently cutting content.

---

## File Handling

Before updating:

1. Read current `CLAUDE.md` into memory
2. Rename it to `CLAUDE_old.md` (or `_old2`, `_old3` if those exist)
3. Write the updated version as new `CLAUDE.md`

This ensures you can diff changes and rollback if needed.

---

## Detection Phase

Scan for changes in these 10 areas. For each, compare the current state against what's in CLAUDE.md.

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

**Inclusion rule:** Include commands used weekly or monthly. Only exclude one-time setup commands. When in doubt, include — a missing command costs more than an extra line.

**Action if changed:** Update the Commands section. Maintain categorization. Place new commands in the correct category.

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
- New "sole importer" pattern (one file owns all access to an external package)

**Action if found:** Add to the "Boundaries" section under Rules. Format: `**\`path/to/file\`** — sole [owner of what]. [What violation looks like].`

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

**Format requirement:** Each gotcha MUST include the fix or workaround, not just the problem description.

**Good:** `**Null bytes in email body** — strip via .replace(/\0/g, '') before INSERT`
**Bad:** `**Null bytes in email body** — can crash PostgreSQL`

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

**Action if changed:** Update the Tech Stack table. Add or modify rows.

### 5. Conventions Changed

Check if any conventions have changed:

- Default branch (still `master`?)
- Runner command (still `bun run`? `uv run python3`?)
- Commit style
- PR workflow (or lack thereof)

**Action if changed:** Update the Conventions section

### 6. Infrastructure Changed

For projects with external services, Docker, or multi-machine setups:

- New machines or services added?
- Ports changed?
- **LLM models added, removed, or renamed?** Check model IDs are verbatim — incorrect model IDs cause silent failures
- New quant variants available?
- Operational constraints changed (auth, concurrency, timeouts)?
- Storage paths or conventions changed?

**Action if changed:** Update the Infrastructure section. Model IDs must be verbatim — copy exact strings from the source (LM Studio, config files, API responses).

### 7. Env Var Reference Changed

Check if new environment variables have been added to task runner commands:

```bash
# Check Justfile for env var patterns
grep -n '\$\{.*:-' Justfile    # Default value patterns
grep -n 'env_var' Justfile      # Justfile env_var() calls
```

**Compare against:** Current "Env Var Reference" table in CLAUDE.md

**Signs of new env vars:**
- New Justfile recipe that reads an environment variable
- Existing recipe modified to accept a new env var
- Env var removed or renamed

**Action if found:** Add row to the Env Var Reference table with Variable, Recipes, and Meaning columns. If the Env Var Reference section doesn't exist yet but env vars are now used, create the section.

### 8. Patterns Changed

Check if new coding conventions have been established:

- New env var access patterns
- New testing patterns
- New error handling conventions
- New file path conventions
- New config hierarchy patterns

**Sources:**
- Conversation context (patterns established this session)
- New utility functions that set conventions
- README or doc updates mentioning conventions

**Action if found:** Add to the "Patterns" section under Rules. Keep each pattern to one line.

### 9. Domain Conventions Changed

Check if new domain-specific patterns have been established:

- New date formats or timezone handling
- New currency or number formatting
- New ID format conventions (ABN, reference numbers, etc.)
- New naming conventions (mailbox names, category slugs, etc.)

**Action if found:** Add to the "Domain Conventions" section under Rules. If the section doesn't exist yet, create it.

### 10. Project Structure Changed

Check if the source directory structure has changed:

```bash
# Compare src/ tree against CLAUDE.md's Project Structure section
ls -la src/
```

**Signs of structural changes:**
- New directory added to `src/`
- Directory renamed or removed
- Significant change in file count within a directory
- New critical files added (schema, entry points)

**Action if changed:** Update the Project Structure tree. Maintain the architecture-depth level — show directories with summaries, individual files only for critical entry points.

---

## Update Actions

For each detected change, update the appropriate section using these templates:

### Adding a New Command

```markdown
### [Existing Category]
```bash
existing-command   # Existing description
new-command        # New description ← ADD THIS
```
```

Place the new command in the correct category. If adding many commands and no category fits, consider creating a new category.

### Adding a New Boundary Rule

```markdown
### Boundaries — Never Cross These

1. [Existing boundary]
2. [Existing boundary]
3. **`src/newmodule/`** — sole [owner]. Never [violation]. ← ADD THIS
```

### Adding a New Gotcha

```markdown
### Gotchas — Things That Have Bitten Us

1. [Existing gotcha]
2. **[Problem]** — [fix/workaround] ← ADD THIS (must include fix!)
```

### Updating Tech Stack

```markdown
## Tech Stack

| Layer | Technology |
|-------|------------|
| ... | ... |
| [New Layer] | [New Technology] | ← ADD THIS
```

### Adding a New Env Var

```markdown
## Env Var Reference

| Variable | Recipes | Meaning |
|----------|---------|---------|
| ... | ... | ... |
| `NEW_VAR` | recipe1, recipe2 | Description ← ADD THIS
```

Also add an example to the usage block if the variable has special quoting needs.

### Adding a Pattern

```markdown
### Patterns — Follow These

1. [Existing pattern]
2. [New convention description] ← ADD THIS
```

### Adding a Domain Convention

```markdown
### Domain Conventions

- [Existing convention]
- [New format/pattern]: description ← ADD THIS
```

### Updating Infrastructure

For new machines:
```markdown
| [New Machine] | [Role] | [Address] | ← ADD ROW
```

For new model IDs (must be verbatim):
```markdown
**LLM Models (verbatim IDs):**
- [existing models]
- `exact/model-id` — description ← ADD THIS
```

For new operational notes:
```markdown
**Operational:**
- [existing notes]
- [New constraint or behavior] ← ADD THIS
```

### Updating Project Structure

```markdown
## Project Structure

```
src/
├── existing/         # existing description
├── newdir/           # N files — purpose description ← ADD THIS
```
```

Update file counts if they've changed significantly. Update critical file listings if new entry points or schema files were added.

### Updating Conventions

```markdown
## Conventions

- [existing conventions]
- **[New convention]:** value ← ADD THIS
```

---

## Anti-Degradation Safeguards

These rules prevent maintenance from eroding the CLAUDE.md quality:

1. **Never remove content unless confirmed stale.** Before removing any line, verify against the source (Justfile, package.json, src/ directory, config files). If the source confirms the content is gone, remove it. Otherwise, keep it.

2. **Preserve all existing entries when updating a section.** Add new ones, modify changed ones, but never trim existing entries "to save space" or "for consistency."

3. **Update stats, don't remove them.** If CLAUDE.md says "512 tests" and there are now 530, update the number. Don't remove the stat entirely.

4. **If the file exceeds the hard maximum after updates, flag to the user.** Do not silently cut content to fit. Say: "CLAUDE.md is now [X] lines, which exceeds the [tier] hard max of [Y]. Would you like to move some content to BOOTSTRAP_PROMPT.md?"

5. **Never restructure sections during maintenance.** Maintenance adds to the existing structure. If the structure needs reworking, that's a regeneration task (use CLAUDE_GENERATOR.md).

6. **Gotchas must keep their fixes.** Never shorten a gotcha to just the problem description. The fix/workaround is the valuable part.

---

## Cross-Reference Guide — What Goes Where

When new content is detected during maintenance, decide where it belongs:

| Content Type | CLAUDE.md | BOOTSTRAP |
|-------------|-----------|-----------|
| Command reference | YES | — |
| Env var reference | YES | — |
| Module boundary (1-line) | YES | Detailed with file:line |
| Gotcha with fix (1-line) | YES | Detailed explanation |
| Model IDs / service details | YES | — |
| Architecture explanation | — | YES |
| "Why" behind a decision | — | YES |
| Domain convention (brief) | YES | YES (detailed) |
| Debugging playbook | — | YES |

**Litmus test:** If a new session needs this to execute a command or avoid breaking something → CLAUDE.md. If it helps understand *why* → suggest BOOTSTRAP_MAINTENANCE.

---

## Suggestion Phase

After updating CLAUDE.md, consider whether other files need updates:

### Suggest BOOTSTRAP_MAINTENANCE if:

- New module boundaries were added (needs detailed explanation)
- New quirks were discovered (needs file:line references)
- Architecture changed significantly
- New key files were added

**Output:**
> **Suggestion:** New module boundaries were added. Consider running BOOTSTRAP_MAINTENANCE to update the module boundaries table and add detailed documentation.

### Suggest EVOLUTION_MAINTENANCE if:

- Major decisions were made this session
- Significant features were completed
- Breaking changes were introduced

**Output:**
> **Suggestion:** A significant decision was made (chose X over Y). Consider running EVOLUTION_MAINTENANCE to document this in the decision log.

### Suggest SESSION_MAINTENANCE if:

- Project state has changed significantly
- Tasks were completed or started
- New blockers discovered

**Output:**
> **Suggestion:** Project state has changed. Consider running SESSION_MAINTENANCE to update the current status.

---

## Quality Checklist

Before presenting the updated CLAUDE.md, verify:

**Anti-degradation**
- [ ] No existing content was removed unless confirmed stale against source files
- [ ] Stats (test counts, recipe counts) are updated, not removed
- [ ] All existing gotchas still have their fix/workaround

**Completeness**
- [ ] Could a new session construct ANY `just`/`make` command from this file alone?
- [ ] Are ALL module ownership boundaries listed?
- [ ] Are ALL env vars that modify command behavior in the reference table?
- [ ] Are service-specific details verbatim (model IDs, ports, quant names)?

**Right-sizing**
- [ ] Within complexity tier budget? If over hard max, flagged to user?
- [ ] Would removing any line require a developer to open a source file?

**Accuracy**
- [ ] New commands are accurate — tested or pulled from actual source files?
- [ ] New gotchas include the fix/workaround, not just the problem?
- [ ] New rules are specific ("never do X in Y") not vague?

**Format**
- [ ] Formatting is consistent with existing sections?
- [ ] Old file backed up as CLAUDE_old.md?

---

## Examples

### Example 1: Adding New Commands

**Detected:** New `just db-seed` command added to Justfile.

**Before:**
```markdown
### Infrastructure
```bash
just db-up        # Start PostgreSQL container
just db-migrate   # Run pending migrations
just db-shell     # Open psql shell
```
```

**After:**
```markdown
### Infrastructure
```bash
just db-up        # Start PostgreSQL container
just db-migrate   # Run pending migrations
just db-shell     # Open psql shell
just db-seed      # Seed database with test data
```
```

---

### Example 2: Adding New Gotcha (with fix)

**Detected:** During this session, discovered that Drizzle Studio crashes if database is empty.

**Before:**
```markdown
### Gotchas — Things That Have Bitten Us

1. **Qwen 3.5 thinking mode** — always send `enable_thinking: false` for JSON extraction
2. **LM Studio bug #1602** — `content` can be empty; fall back to `reasoning_content`
```

**After:**
```markdown
### Gotchas — Things That Have Bitten Us

1. **Qwen 3.5 thinking mode** — always send `enable_thinking: false` for JSON extraction
2. **LM Studio bug #1602** — `content` can be empty; fall back to `reasoning_content`
3. **Drizzle Studio crashes on empty DB** — run `just db-seed` first or it fails silently
```

---

### Example 3: Adding New Module Boundary

**Detected:** New `src/export/` module created for PDF/CSV export functionality.

**Before:**
```markdown
### Boundaries — Never Cross These

1. **`src/lib/db.ts`** — sole importer of `postgres`. All DB access goes through here.
2. **`src/lib/llm.ts`** — sole importer of `openai`. All LLM calls go through here.
```

**After:**
```markdown
### Boundaries — Never Cross These

1. **`src/lib/db.ts`** — sole importer of `postgres`. All DB access goes through here.
2. **`src/lib/llm.ts`** — sole importer of `openai`. All LLM calls go through here.
3. **`src/export/`** — sole owner of file generation. Never create PDFs/CSVs elsewhere.
```

**Also suggest:**
> **Suggestion:** New module `src/export/` was added. Consider running BOOTSTRAP_MAINTENANCE to add it to the module boundaries table with full details.

---

### Example 4: Adding New Env Var

**Detected:** New `BATCH_SIZE` env var added to `just classify` in Justfile.

**Before:**
```markdown
## Env Var Reference

| Variable | Recipes | Meaning |
|----------|---------|---------|
| `FY` | ingest, filter | Financial year (e.g. FY2024-25) |
| `RUN_ID` | ingest-rollback, classify-rollback | Run UUID |
```

**After:**
```markdown
## Env Var Reference

| Variable | Recipes | Meaning |
|----------|---------|---------|
| `FY` | ingest, filter | Financial year (e.g. FY2024-25) |
| `RUN_ID` | ingest-rollback, classify-rollback | Run UUID |
| `BATCH_SIZE` | classify | Emails per LLM batch (default: 20) |
```

---

### Example 5: Updating Infrastructure (New Model)

**Detected:** New LLM model `qwen/qwen3.5-14b` added to LM Studio.

**Before:**
```markdown
**LLM Models (verbatim IDs):**
- `qwen/qwen3.5-27b` — primary, best accuracy
- `qwen/qwen3.5-9b` — fallback, faster
```

**After:**
```markdown
**LLM Models (verbatim IDs):**
- `qwen/qwen3.5-27b` — primary, best accuracy
- `qwen/qwen3.5-14b` — mid-range (accuracy/speed balance)
- `qwen/qwen3.5-9b` — fallback, faster
```

---

### Example 6: Adding Domain Convention

**Detected:** New TFN (Tax File Number) format used in signal extraction.

**Before:**
```markdown
### Domain Conventions

- Dates: Australian DD/MM/YYYY, financial year Jul 1 → Jun 30 (`FY2024-25`)
- Currency: AUD patterns (`$1,234.56`, `-$50`, `($50)`, CR suffix → negative)
- ABN: 11-digit Australian Business Number, validated via modulus 89
```

**After:**
```markdown
### Domain Conventions

- Dates: Australian DD/MM/YYYY, financial year Jul 1 → Jun 30 (`FY2024-25`)
- Currency: AUD patterns (`$1,234.56`, `-$50`, `($50)`, CR suffix → negative)
- ABN: 11-digit Australian Business Number, validated via modulus 89
- TFN: 9-digit Tax File Number, validated via modulus 11 algorithm
```

---

### Example 7: Updating Project Structure

**Detected:** New `src/reports/` directory added with 3 files.

**Before:**
```markdown
## Project Structure

```
src/
├── config/           # 2 files — dynamic config from .env
├── db/               # 2 files — schema.sql + migrate.ts
├── lib/              # 14 core modules
└── scripts/          # 9 CLI entry points
```
```

**After:**
```markdown
## Project Structure

```
src/
├── config/           # 2 files — dynamic config from .env
├── db/               # 2 files — schema.sql + migrate.ts
├── lib/              # 14 core modules
├── reports/          # 3 files — accountant package generation
└── scripts/          # 9 CLI entry points
```
```

---

### Example 8: No Changes Detected

**Detected:** No changes to commands, boundaries, gotchas, tech stack, env vars, infrastructure, or conventions.

**Output:**
> **No CLAUDE.md updates needed.**
>
> I checked for changes to:
> - Commands (Justfile, package.json) — no changes
> - Module boundaries — no new modules
> - Gotchas — no new issues discovered
> - Tech stack — no dependency changes
> - Env var reference — no new variables
> - Infrastructure — no service changes
> - Domain conventions — unchanged
> - Project structure — unchanged
> - Conventions — unchanged
>
> CLAUDE.md remains current. No backup created since no changes were made.

---

## After Update

Once you've updated `CLAUDE.md`, inform the user:

> **CLAUDE.md updated** ([X] lines, [+Y/-Z] net change, complexity tier: [simple/medium/complex]).
>
> **Changes made:**
> - [List each change, e.g., "Added `just db-seed` to Infrastructure commands"]
> - [List each change, e.g., "Added gotcha: Drizzle Studio crashes on empty DB"]
>
> **Anti-degradation check:** [Confirmed no content removed / Removed N stale entries (listed)]
>
> **Backup:** Previous version saved as `CLAUDE_old.md`
>
> [Include any suggestions for other maintenance prompts]
