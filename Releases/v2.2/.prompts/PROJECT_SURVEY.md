# PROJECT_SURVEY.md
<!-- Version: 2.2 -->

> **What this file does:** Thoroughly surveys an entire codebase **and cross-references Claude Code's project memory** to build comprehensive understanding before generating documentation. Run this ONCE at the start — it's expensive but creates the foundation for all subsequent generators.
>
> **How to run:** Prompt Claude Code with: `"Read .prompts/PROJECT_SURVEY.md and follow its instructions"`
>
> **Output:** Creates `.prompts/PROJECT_SURVEY_OUTPUT.md` (enriched with institutional knowledge from memory when available) and keeps the survey in context for subsequent generators.

---

## Instructions for Claude Code

You are about to survey this project thoroughly. This will consume significant context, but the goal is to understand the project deeply enough that the generated documentation (CLAUDE.md, BOOTSTRAP_PROMPT.md, SESSION_HANDOFF.md, EVOLUTION.md) will be accurate and comprehensive. If Claude Code project memory exists for this project, you will also cross-reference those memories against your codebase findings to capture institutional knowledge that code alone cannot reveal — war stories, architecture rationale, debugging playbooks, operational workflows, and hard-won quirks.

**Work through all 6 phases sequentially. Do not skip phases.** Phase 5 (Memory Cross-Reference) may be skipped only if no project memory directory exists.

After completing all phases, write the complete survey to `.prompts/PROJECT_SURVEY_OUTPUT.md`. If that file already exists, rename it to `PROJECT_SURVEY_OUTPUT_old.md` (or `_old2`, `_old3` if those exist).

---

## Exclusion Rules

**Never read or survey these:**

| Exclude | Reason |
|---------|--------|
| `.prompts/` folder | Contains generator/maintenance docs, not project code |
| `*_old*` files | Backup files from previous generations |
| `node_modules/` | Dependencies, not project code |
| `.git/` | Git internals |
| `dist/`, `build/`, `.next/`, `.astro/` | Build outputs |
| `__pycache__/`, `*.pyc`, `.pytest_cache/` | Python cache |
| `.venv/`, `venv/`, `.uv/` | Virtual environments |
| `*.lock` files | Lockfiles (note their existence, don't read contents) |
| `.env` files | May contain secrets (note their existence, don't read values) |
| Binary files | Images, PDFs, compiled assets |
| `coverage/`, `.coverage` | Test coverage outputs |

**Always respect `.gitignore`** — if a file/folder is gitignored, exclude it from the survey.

---

## Phase 1: Project Detection

### 1.1 Project Type
Determine if this is:
- **Greenfield** — Few or no source files, minimal structure, just getting started
- **Brownfield** — Existing codebase with established patterns and history

### 1.2 Existing Documentation
Check for and note the presence of:
- [ ] `CLAUDE.md` (project root)
- [ ] `README.md`
- [ ] `docs/` folder and its contents
- [ ] `BOOTSTRAP_PROMPT.md` or similar onboarding doc
- [ ] `SESSION_HANDOFF.md` or similar state doc
- [ ] `ARCHITECTURE.md` or similar design doc
- [ ] `CHANGELOG.md` or `HISTORY.md`
- [ ] `CONTRIBUTING.md`
- [ ] `.claude/` folder (Claude Code skills/commands)

### 1.3 Tech Stack Markers
Identify the primary tech stack by checking for:

| Marker File | Indicates |
|-------------|-----------|
| `pyproject.toml` | Python (modern, uv/poetry/pip) |
| `requirements.txt` | Python (pip) |
| `setup.py` | Python (legacy) |
| `package.json` | Node.js / JavaScript / TypeScript |
| `bun.lockb` | Bun runtime |
| `pnpm-lock.yaml` | pnpm package manager |
| `yarn.lock` | Yarn package manager |
| `Cargo.toml` | Rust |
| `go.mod` | Go |
| `Package.swift` | Swift |
| `*.xcodeproj` / `*.xcworkspace` | iOS/macOS Xcode project |
| `astro.config.*` | Astro framework |
| `next.config.*` | Next.js framework |
| `vite.config.*` | Vite bundler |
| `docker-compose.yml` | Docker multi-container |
| `Dockerfile` | Docker single container |
| `Justfile` | Just command runner |
| `Makefile` | Make build system |
| `tsconfig.json` | TypeScript |
| `.nvmrc` | Node version specification |
| `.python-version` | Python version (pyenv) |
| `.tool-versions` | asdf version manager |
| `drizzle.config.*` | Drizzle ORM |
| `prisma/schema.prisma` | Prisma ORM |

---

## Phase 2: Codebase Survey

### 2.1 Directory Structure
Map the top-level directory structure. For each directory, note:
- What it contains (source code, tests, docs, config, assets)
- Approximate file count
- Primary language/purpose

### 2.2 Entry Points
Identify the main entry points:
- **CLI tools:** `main.py`, `cli.py`, `__main__.py`, `bin/` scripts
- **Web apps:** `src/index.*`, `app.py`, `main.ts`, `pages/`, `routes/`
- **Libraries:** `src/lib/`, `src/index.*`, `__init__.py`
- **Extensions:** `manifest.json` (browser), `package.json` (VS Code)

### 2.3 Frameworks & Runtimes
Identify:
- **Language runtime:** Python 3.x, Node.js, Bun, Deno, etc.
- **Web framework:** Astro, Next.js, FastAPI, Flask, Express, etc.
- **ORM/Database:** Drizzle, Prisma, SQLAlchemy, etc.
- **Testing framework:** pytest, vitest, bun:test, jest, etc.
- **Build tools:** Vite, esbuild, webpack, etc.

### 2.4 External Services
Look for integrations with:
- **LLM APIs:** OpenAI, Anthropic, local LLM (LM Studio, Ollama)
- **Databases:** PostgreSQL, SQLite, Redis, Qdrant
- **Cloud services:** AWS, GCP, Cloudflare
- **Auth providers:** Better Auth, Auth.js, Clerk
- **Payment:** Stripe, Paddle
- **Monitoring:** Sentry, Langfuse

Check `.env.example`, `docker-compose.yml`, and config files for service references.

---

## Phase 3: Infrastructure Discovery

### 3.1 Single vs Multi-Machine
Determine the deployment topology:

**Indicators of multi-machine setup:**
- SSH config references in documentation
- Multiple hostnames/IPs in `.env` or config files
- `docker-compose.yml` with external service URLs
- References to "server", "remote", "LAN", specific IP addresses
- Separate services on different ports/machines

**If multi-machine, document:**
- Dev machine (where Claude Code runs)
- Other machines and their roles (LLM inference, database, production)
- Network topology (LAN IPs, ports, hostnames)
- How machines communicate (SSH, HTTP API, database connections)

### 3.2 Docker Configuration
If Docker is used:
- List all services in `docker-compose.yml`
- Note exposed ports and volume mounts
- Identify which containers run locally vs remotely
- Note any container naming conventions

### 3.3 Environment Configuration
Note (but don't read values):
- Which `.env` files exist (`.env`, `.env.local`, `.env.development`, etc.)
- What environment variables are expected (from `.env.example` or code)
- Any machine-specific or environment-specific configuration

---

## Phase 4: Pattern Recognition

### 4.1 Module Boundaries
Identify logical modules and their ownership:
- Look for clear folder separation (`src/auth/`, `src/api/`, `src/db/`)
- Identify wrapper modules (thin wrappers around SDKs/libraries)
- Note any circular dependency handling or import restrictions
- Look for barrel files (`index.ts`, `__init__.py`) that define public APIs

**For each major module, document:**
- What it owns (sole responsibility)
- What it should never import (isolation boundaries)
- Key exports/functions

### 4.2 Git History Insights
Scan recent git history (last 50-100 commits) for:
- Commits containing "fix", "bug", "workaround", "hack" — these reveal quirks
- Commits containing "refactor", "restructure" — these reveal architecture changes
- Commits containing "decision", "chose", "instead of" — these reveal design choices
- Frequent touches to the same files — these reveal complexity hotspots

### 4.3 Code Comment Patterns
Search for comments containing:
- `NOTE:`, `IMPORTANT:` — Developer warnings
- `TODO:`, `FIXME:` — Known issues
- `HACK:`, `WORKAROUND:` — Technical debt
- `BUG:`, `XXX:` — Known problems

**For each found, note:**
- File and line number
- The issue or quirk described
- Whether it's been resolved (check git blame)

### 4.4 Testing Patterns
Identify:
- Test file locations (`tests/`, `__tests__/`, `*.test.*`, `*.spec.*`)
- Test framework and configuration
- Approximate test count
- Any notable test patterns (fixtures, factories, mocks)

---

## Phase 5: Memory Cross-Reference

> **EARLY EXIT CHECK**
>
> Before reading further, determine the memory path:
> - Encode the project's absolute path by replacing each `/` with `-`
> - Check if `~/.claude/projects/{encoded-path}/memory/` exists
>
> **If the directory does NOT exist:**
> 1. Record in your context: `PHASE_5_STATUS: SKIPPED — no memory directory found`
> 2. Skip the rest of Phase 5 entirely
> 3. Proceed directly to Phase 6
> 4. In the final output, omit the Memory Cross-Reference Summary section
> 5. In the completion message, state: "No project memory directory found — output based on codebase survey only."
>
> **If the directory exists:** Continue with Section 5.0 below.

---

This phase captures institutional knowledge that code analysis alone cannot reveal: *why* things are the way they are, *what went wrong* getting there, and *how to operate* the system. In real-world testing, this phase added ~30-50% more content to the survey output.

### Phase 5 State Tracking

Maintain the following state as you progress through Phase 5. Update it after completing each subsection.

```
MEMORY_PATH: [resolved path]
MEMORY_INDEX_SOURCE: [MEMORY.md / fallback scan / both]

FILES_FOUND: [count]
FILES_BY_TYPE:
  - project: [count]
  - reference: [count]
  - user: [count]
  - feedback: [count]
CATEGORIZATION_METHODS:
  - frontmatter: [count]
  - filename: [count]
  - content inference: [count]

FILES_READ: [count]
FILES_CONTRIBUTING_NEW_INFO: [count]
FILES_STALE_OR_CONTRADICTORY: [count]

MERGE_PLAN:
  - inline_enrichments: [count]
  - dedicated_sections: [list of section names]
  - folded_to_quirks: [count]

CONDITIONAL_SECTIONS:
  - include: [list]
  - fold: [list]
  - omit: [list]

ESTIMATED_CONTRIBUTION: [X]%
ESTIMATION_METHOD: [precise / heuristic]
```

Reference this state when:
- Writing the Memory Cross-Reference Summary (Section 6.1)
- Constructing the completion message
- Making inclusion decisions for conditional sections

### 5.0 Locate Project Memory
Determine the project memory path:
- Claude Code stores project memories at `~/.claude/projects/{encoded-project-path}/memory/`
- The encoded path replaces each `/` in the absolute project path with `-`
- Example: project at `/Users/dev/code/my-app` → memory at `~/.claude/projects/-Users-dev-code-my-app/memory/`
- Check if the directory exists and contains a `MEMORY.md` file
- **If the directory does not exist, skip this entire phase** and proceed to Phase 6

### 5.1 Read Memory Index
Read `MEMORY.md` from the memory directory. This file indexes all memories:
```
- [Title](filename.md) — one-line description
```
Note the total count of memory files listed.

**Fallback Discovery:**

If `MEMORY.md` is missing, incomplete, or appears outdated (references files that don't exist), scan the memory directory directly for common filenames:

- `project_overview.md`
- `project_architecture_decisions.md`
- `project_*_war_stories.md`
- `project_debugging_playbook.md`
- `project_*_quirks.md`
- `project_benign_warnings.md`
- `project_cli_patterns.md`
- `project_shutdown_and_resume.md`
- `project_data_state.md`
- `project_*_workflows.md`
- `user_*.md`
- `reference_*.md`
- `feedback_*.md`

Process any matching files found, even if not indexed in MEMORY.md. Note in the Memory Cross-Reference Summary if discovery relied on fallback scanning.

### 5.2 Categorize by Type

Read each memory file's first 10 lines. Determine the categorization method:

**Scenario A: Valid YAML Frontmatter with `type` Field**

Look for YAML frontmatter delimited by `---`:
```yaml
---
type: project
name: Some memory name
description: One-line description
---
```

If the `type` field exists and contains a valid value (`project`, `reference`, `user`, or `feedback`), use it directly.

**Scenario B: YAML Frontmatter Without `type` Field**

If frontmatter exists but lacks a `type` field:
1. Check for other hint fields: `category`, `kind`, `scope`
2. If none found, apply filename-based categorization (Section 5.2.1)

**Scenario C: Malformed or Missing Frontmatter**

If no valid frontmatter is detected (no `---` delimiters, or parsing fails):
1. First, apply filename-based categorization (Section 5.2.1)
2. If filename doesn't match patterns, read the first paragraph and infer:
   - Contains "architecture", "decision", "chose", "design", "why we" → `project`
   - Contains "documentation", "reference", "link", "resource" → `reference`
   - Contains "preference", "environment", "setup", "my " → `user`
   - Contains "workflow preference", "commit style", "tooling" → `feedback`
   - Default if no signals → `project` (highest value assumption)

**Record the categorization method used for each file** (frontmatter / filename / content inference) — this will be noted in the Memory Cross-Reference Summary.

**Type reference:**

| Type | Contains | Survey Relevance |
|------|----------|-----------------|
| `project` | Architecture decisions, war stories, workflows, data state, quirks | Highest value — directly enriches survey with rationale and operational knowledge |
| `reference` | Documentation structure, external resources, conventions | Enriches Existing Documentation and Recommendations sections |
| `user` | Project owner info, environment, security preferences | Enriches Infrastructure and Project Overview sections |
| `feedback` | User preferences for workflow, commits, tooling | Enriches Recommendations section |

**Read `project` type memories first** — they contain ~80% of the unique value.

### 5.2.1 Fallback: Filename-Based Categorization

If memory files lack YAML frontmatter or the `type` field is missing, infer type from filename patterns:

| Filename Pattern | Inferred Type |
|------------------|---------------|
| `project_*.md`, `*_architecture*.md`, `*_war_stories*.md`, `*_quirks*.md`, `*_debugging*.md`, `*_workflows*.md`, `*_data_state*.md` | `project` |
| `*_docs*.md`, `*_reference*.md`, `*_conventions*.md`, `*_resources*.md` | `reference` |
| `user_*.md`, `*_environment*.md`, `*_preferences*.md`, `*_setup*.md` | `user` |
| `feedback_*.md`, `*_workflow_prefs*.md`, `*_tooling*.md` | `feedback` |
| Unmatched patterns | Treat as `project` (highest value default) |

Apply this fallback per-file. A memory directory may contain a mix of files with and without frontmatter — use frontmatter when available, filename patterns when not.

### 5.3 Read and Cross-Reference Each Memory
For each memory file (prioritised by type: project → reference → user → feedback):

1. **Read the full content** (not just frontmatter)
2. **Compare against Phases 1-4 findings** — identify what this memory adds that the codebase survey alone didn't capture
3. **Classify each enrichment** into one or more of these categories, noting the target section:

| Enrichment Category | What to Look For | Target Section |
|---------------------|------------------|----------------|
| Architecture Decisions | Design rationale with "why" explanations, trade-offs, constraints, rejected alternatives | **Architecture Decisions** (dedicated) or **Module Boundaries** (inline if <3 decisions) |
| War Stories / Gotchas | Bugs found in production, cascading failures, defensive patterns explained by past incidents | **Quirks & Gotchas** (always inline) |
| Operational Workflows | Multi-step processes (e.g., iterative rule proposal cycles), priority strategies, command sequences | **Operational Workflows** (dedicated) |
| Pipeline / Data State | Current processing volumes, per-component breakdowns, status percentages, run IDs | **Pipeline / Data State** (dedicated) |
| Domain-Specific Mappings | Virtual fields, view vs table distinctions, naming translations, field aliases | **Domain-Specific Mappings** (dedicated) or **Quirks & Gotchas** (if <3 mappings) |
| Debugging Playbook | Triage order, known causes per component, diagnostic commands, "check this first" lists | **Debugging Playbook** (dedicated) or **Quirks & Gotchas** (if <2 steps) |
| Benign Warnings | Expected console output that looks alarming but is handled gracefully in code | **Benign Warnings** (dedicated) or **Quirks & Gotchas** (if <2 warnings) |
| Shutdown / Resume | Graceful stop patterns (SIGINT handling), state persistence, rollback procedures, `--resume` flags | **Shutdown / Resume Patterns** (dedicated) or **Quirks & Gotchas** (if only 1 component) |
| Env Config Details | Configuration hierarchy, per-user/per-component structure, secret management patterns | **Env Config Hierarchy** (dedicated if complex) or omit (if trivial) |
| Attachment / File Handling | Size limits, dedup strategies, filters, storage paths, extraction rules | **Attachment / File Handling** (dedicated) |
| CLI Patterns | Flag parsing quirks, quoting issues, command workarounds, ad-hoc patterns across scripts | **CLI Gotchas** (dedicated) or **Quirks & Gotchas** (if <2 patterns) |
| Test Insights | Skip conditions for external deps, factory patterns, environment-dependent behaviour | **Testing** (inline enrichment) |

Use this table during Phase 5.3 to immediately classify where each enrichment belongs. The target section column eliminates ambiguity when constructing the merge plan in Section 5.4.

4. **Note which existing survey section** each enrichment extends, or whether it requires a new dedicated section

### 5.3.1 Conflict Resolution

When memory content contradicts Phase 1-4 findings, apply these resolution rules:

| Conflict Type | Detection | Resolution |
|---------------|-----------|------------|
| **Removed files/modules** | Memory references paths that don't exist in current codebase | Mark memory as stale; do not include in output; note in Recommendations: "Memory X references removed module Y" |
| **Architecture drift** | Memory describes architecture that code contradicts (e.g., "we use Redis" but no Redis in code) | Trust code for current state; note discrepancy in Quirks & Gotchas: "Memory/code drift: [description] — verify if memory is outdated or code changed" |
| **Outdated dependencies** | Memory references old versions or deprecated packages | Trust code for current versions; memory may still provide historical context for *why* a migration happened |
| **Abandoned workflows** | Memory describes workflows that git history shows were removed/replaced | Omit from Operational Workflows; optionally note in Recommendations: "Memory describes workflow X which appears abandoned per commit [hash]" |
| **Contradictory memories** | Two memory files contradict each other | Prefer the more recently modified file; note the conflict in Recommendations |

**Core Principle:**
- **Code is ground truth** for *what currently exists*
- **Memory is authoritative** for *why decisions were made* and *what was learned* — but only when it aligns with current code state
- When in doubt, include a note about the discrepancy rather than silently choosing one source

**Track conflicts:** Increment `FILES_STALE_OR_CONTRADICTORY` in your state tracking for each memory file with detected conflicts.

### 5.4 Merge Plan

Before writing output, construct the following merge plan **in your working context**. This is not written to a file — it's your internal organization before Phase 6.

**Merge Plan Format:**

```
INLINE ENRICHMENTS:
- [Source memory file] → [Existing section to enrich]
  Content: [Brief description of what to add]

NEW DEDICATED SECTIONS (3+ points each):
- [Section name]
  Source: [Memory file(s)]
  Points: [Count]
  Summary: [One-line description]

FOLDED INTO QUIRKS & GOTCHAS (<3 points):
- [Content description] ← [Source memory file]
```

**Rules:**
1. Every enrichment identified in Section 5.3 must appear in exactly one category
2. Dedicated sections require ≥3 bullet points of content; otherwise fold into Quirks & Gotchas
3. Inline enrichments add to existing Phase 1-4 sections without creating new headings
4. If a memory file contributes to multiple categories, list it multiple times with different content

**Do not proceed to Phase 6 until the merge plan accounts for all memory enrichments.**

### 5.5 Quantify Memory Contribution
Count and record:
- Total memory files read
- Memory files that contributed new information (not already captured in Phases 1-4)
- Number of new dedicated sections to be added
- Number of existing sections to be enriched
- Estimated percentage of total output content that comes from memory vs codebase survey

These counts will be included in the Memory Cross-Reference Summary section and reported in the completion message.

**Estimating Memory Contribution Percentage:**

Method 1 — Precise counting (preferred for complex outputs):
1. Count total substantive items in final output: bullet points + table rows + paragraphs in all sections
2. Count items that originated from memory (not discoverable from Phases 1-4 alone)
3. Calculate: `(memory-originated items) / (total items) x 100`

Method 2 — Heuristic estimation (acceptable for simpler outputs):

| Scenario | Estimated % |
|----------|-------------|
| 0 dedicated memory sections added, minimal inline enrichment | <10% |
| 1-2 dedicated memory sections, light inline enrichment | 15-25% |
| 3-4 dedicated memory sections, moderate inline enrichment | 30-40% |
| 5+ dedicated memory sections, extensive inline enrichment | 40-50%+ |

**Round to nearest 5%.** The goal is a useful signal, not false precision.

Record which method was used in the Memory Cross-Reference Summary.

### 5.6 Memory Coverage Assessment

Evaluate memory coverage using these criteria:

| Rating | Criteria |
|--------|----------|
| **Sparse** | <5 memory files total, OR >50% of major modules (identified in Phase 4.1) have no corresponding memory coverage |
| **Stale** | Memory files exist but reference outdated content: removed files, deprecated architecture, superseded decisions, or old dependency versions. Cross-check against Phase 4.2 git history — if major refactors occurred after memory file dates, memories are likely stale. |
| **Comprehensive** | ≥10 memory files covering architecture, workflows, quirks, and operational knowledge; <20% of major modules lack memory coverage; no significant staleness detected |

**In the Recommendations section, specify:**
- The rating (sparse/stale/comprehensive)
- Specific gaps: "No memory exists for: [module/area]"
- Staleness details: "Memory file X references deprecated Y"
- Suggested memories to create: "Recommend creating memory for: [topic]"

### 5.7 Memory Health Assessment

For each major module identified in Phase 4.1, assess memory coverage:

1. Does any memory file cover this module?
2. If yes, is the coverage current (aligns with code) or stale?
3. If no, would memory for this module be valuable?

**Criteria for recommending new memories:**
- Module has complex logic that isn't self-documenting
- Module has known quirks discovered during Phase 4.3 (code comments)
- Module has frequent git touches (Phase 4.2 complexity hotspots)
- Module integrates with external services (Phase 2.4)
- Module has non-obvious operational requirements

**Prioritize recommendations** — list the most valuable missing memories first.

This assessment populates the **Memory Health** section in the output (Section 6.1).

---

## Phase 6: Output

### 6.0 Conditional Section Checklist

Before writing output, evaluate each memory-derived section against its inclusion threshold. Mark each as **INCLUDE**, **OMIT**, or **FOLD** (into Quirks & Gotchas).

| Section | Include If | Fold If | Omit If |
|---------|------------|---------|---------|
| Architecture Decisions | ≥1 decision with documented rationale | — | No decisions found |
| Pipeline / Data State | Any current volumes, breakdowns, or status data | — | No pipeline/data state info |
| Operational Workflows | ≥1 multi-step workflow documented | — | No workflows found |
| Debugging Playbook | ≥2 diagnostic steps or triage procedures | 1 step only | No debugging info |
| Benign Warnings | ≥2 warning patterns documented | 1 warning only | No warnings found |
| Env Config Hierarchy | Non-trivial hierarchy (≥3 levels OR ≥5 variables) | — | Simple/trivial config |
| Domain-Specific Mappings | ≥3 field mappings or translations | 1-2 mappings | No mappings found |
| Attachment / File Handling | Any size limits, dedup rules, or storage patterns | — | No file handling info |
| Shutdown / Resume Patterns | ≥2 components with interrupt handling | 1 component only | No shutdown info |
| CLI Gotchas | ≥2 gotchas not already in Quirks & Gotchas | 1 gotcha only | None found |
| Memory Cross-Reference Summary | Phase 5 was executed | — | Phase 5 was skipped |
| Memory Health | Phase 5 was executed AND ≥1 module identified in Phase 4.1 | — | Phase 5 was skipped |

**Record your decisions before proceeding to Section 6.1.** The output template should only include sections marked INCLUDE. Content marked FOLD should be added to the Quirks & Gotchas section.

### 6.1 Write Survey Output
Create `.prompts/PROJECT_SURVEY_OUTPUT.md` with the following structure:

```markdown
# Project Survey Output
<!-- Generated: [DATE] -->

## Project Overview
- **Type:** [Greenfield/Brownfield]
- **Primary Language:** [Language]
- **Framework:** [Framework]
- **Description:** [2-3 sentence description based on what you found]

## Tech Stack Summary
| Layer | Technology |
|-------|------------|
| Runtime | ... |
| Framework | ... |
| Database | ... |
| ORM | ... |
| Testing | ... |
| Build | ... |

## Infrastructure
- **Topology:** [Single machine / Multi-machine]
- **Dev Machine:** [Description]
- **Other Machines:** [If applicable]
- **Docker:** [Yes/No, brief description]

## Directory Structure
[Tree structure with annotations]

## Module Boundaries
| Module | Owns | Never Import |
|--------|------|--------------|
| ... | ... | ... |

## Key Files
| File | Purpose | Key Exports |
|------|---------|-------------|
| ... | ... | ... |

## Quirks & Gotchas Found
1. [File:line] — [Description of quirk]
2. ...

## Git History Insights
- Recent focus areas: ...
- Notable decisions: ...
- Complexity hotspots: ...

## External Services
| Service | Purpose | Location |
|---------|---------|----------|
| ... | ... | ... |

## Existing Documentation
- [List of existing docs found and their quality/coverage]

## Testing
- **Framework:** ...
- **Location:** ...
- **Approximate Count:** ...
- **Notable Patterns:** ...

## Architecture Decisions
| Decision | Rationale |
|----------|-----------|
| ... | Why this choice was made, what alternatives were rejected |

## Pipeline / Data State
- **Total records/items:** [count]
- **Per-component breakdown:** [table if applicable]
- **Current processing status:** [what's done, what remains]

## Operational Workflows
**[Workflow name]:**
1. [Step 1]
2. [Step 2]
...
- **Results:** [what this workflow achieved]
- **Priority/ordering strategy:** [if applicable]

## Debugging Playbook
1. **[First check]** — [diagnostic command]. Common causes: ...
2. **[Second check]** — ...

## Benign Warnings
- `[warning text pattern]` — [why it's benign and how it's handled]

## Env Config Hierarchy
[Tree or table showing configuration hierarchy]

## Domain-Specific Mappings
| Public Name | Maps To | Notes |
|-------------|---------|-------|
| ... | ... | ... |

## Attachment / File Handling
- **Max size:** ...
- **Dedup strategy:** ...
- **Storage path pattern:** ...

## Shutdown / Resume Patterns
- **[Component]:** [how it handles interruption and resume]

## CLI Gotchas
- **[Script/command]:** [the gotcha and workaround]

## Memory Cross-Reference Summary
- **Memory files found:** [N]
- **Files contributing new information:** [M] of [N]
- **New sections added from memory:** [list of section names]
- **Existing sections enriched:** [list of section names]
- **Estimated memory contribution:** ~[X]% of total output content

## Memory Health

**Overall Rating:** [sparse/stale/comprehensive per Section 5.6]

**Coverage by Module:**
| Module | Has Memory? | Quality | Notes |
|--------|-------------|---------|-------|
| [Module from Phase 4.1] | Yes/No | Good/Stale/Missing | [Brief note] |
| ... | ... | ... | ... |

**Recommended Memories to Create:**
1. **[Topic]** — [Why this memory would be valuable]
   - Suggested filename: `project_[topic].md`
   - Should contain: [Key information to capture]
2. ...

**Stale Memories to Update:**
1. **[Filename]** — [What's outdated and what needs updating]
2. ...

## Recommendations
- [Any immediate observations about documentation gaps]
- [Suggestions for CLAUDE.md content]
- [Suggestions for BOOTSTRAP_PROMPT.md content]
- **Memory coverage:** [sparse/stale/comprehensive per Section 5.6 criteria]
  - Gaps: [specific modules/areas lacking memory]
  - Staleness: [any stale memories identified]
  - Suggested new memories: [topics that should have memory created]
```

### 6.2 Keep in Context
After writing the output file, keep the survey information in your context. The subsequent generator prompts (CLAUDE_GENERATOR.md, BOOTSTRAP_GENERATOR.md, etc.) will rely on this survey to generate accurate documentation.

---

## Examples

### Example 1: Single-Machine Python CLI

**Survey would find:**
- `pyproject.toml` with uv configuration
- `src/cli/` as entry point
- `src/lib/` with utility modules
- pytest in `tests/`
- No Docker, no external services
- Single developer machine

**Key output sections:**
- Simple tech stack table
- Clear module boundaries (cli vs lib)
- Testing conventions
- Commands from Justfile

---

### Example 2: Multi-Machine Astro + LLM Setup

**Survey would find:**
- `astro.config.mjs` with SSR mode
- `package.json` with Bun
- `docker-compose.yml` with PostgreSQL
- `.env` references to remote LLM endpoint (e.g., `192.168.1.56:1234`)
- `drizzle.config.ts` for database
- Multi-machine: Mac Mini (dev) + RTX 3090 PC (LLM inference)

**Key output sections:**
- Infrastructure diagram with both machines
- LLM endpoint configuration
- Docker services (Postgres)
- Module boundaries (api, db, llm, ui)
- LLM-specific quirks (model loading times, /no_think flag)

**If project memories exist, Phase 5 would additionally surface:**
- Architecture decision rationale (why multi-machine, why specific LLM model)
- LLM-specific debugging playbook from operational experience
- Expected console warnings during LLM inference
- Data processing volumes and pipeline state
- Operational workflows (e.g., iterative rule proposal cycles)

---

### Example 3: Browser Extension + Python Backend

**Survey would find:**
- `manifest.json` (Chrome MV3 extension)
- `pyproject.toml` (Python orchestration layer)
- macOS-specific code (Accessibility API, PyObjC)
- No Docker, all local
- Extension in `extension/`, Python in `src/`

**Key output sections:**
- Two-part architecture (extension + Python)
- Platform-specific gotchas (macOS Accessibility permissions)
- MV3 extension limitations
- Module boundaries (extension vs orchestrator vs utilities)
- PyObjC import quirks

---

### Example 4: Cross-Platform Node.js Tool

**Survey would find:**
- `package.json` with Node.js
- `tsconfig.json` for TypeScript
- Platform detection code in `src/platform/`
- Different code paths for macOS, Windows, Linux
- Tests for each platform

**Key output sections:**
- Platform-specific modules and their boundaries
- Testing strategy for cross-platform
- Build process for each platform
- Known platform-specific quirks

---

### Example 5: Project With Rich Memory History

**Phases 1-4 (codebase survey) would find:**
- Standard codebase findings: tech stack, modules, tests, git history, etc.
- Code comments and git history provide some context
- Survey output is solid but focused on *what is*

**Phase 5 (memory cross-reference) would additionally surface:**
- 25+ project memory files covering architecture decisions, war stories, workflows
- Why specific defensive patterns exist (from production bug memories)
- Iterative workflows that resolved large portions of work without expensive processing
- Debugging triage order based on real operational experience
- Benign warnings that look like errors but are safely handled
- Current data volumes and per-component breakdowns
- CLI flag parsing gotchas discovered through real usage
- Shutdown/resume patterns for long-running processes

**Result:** Survey output grows ~30-50% from memory enrichment, capturing institutional knowledge that code alone cannot reveal. The difference is *what is* vs *why it is and what went wrong getting there*.

---

### Example 5a: Memory Processing Trace

This example shows the complete Phase 5 execution for a project with moderate memory history.

**Step 1: Early Exit Check**
- Project path: `/Users/dev/projects/invoice-processor`
- Encoded path: `-Users-dev-projects-invoice-processor`
- Memory path: `~/.claude/projects/-Users-dev-projects-invoice-processor/memory/`
- **Result:** Directory exists → continue with Phase 5

**Step 2: Initialize State Tracking**
```
MEMORY_PATH: ~/.claude/projects/-Users-dev-projects-invoice-processor/memory/
MEMORY_INDEX_SOURCE: [pending]
FILES_FOUND: [pending]
...
```

**Step 3: Read Memory Index (5.1)**
- `MEMORY.md` exists and contains:
  ```
  - [Project Overview](project_overview.md) — Invoice processing system goals
  - [Architecture](project_architecture_decisions.md) — Why PostgreSQL over MongoDB
  - [PDF War Stories](project_pdf_war_stories.md) — Parsing edge cases
  - [Debugging](project_debugging_playbook.md) — Triage for failed invoices
  ```
- Files listed: 4
- Update state: `MEMORY_INDEX_SOURCE: MEMORY.md`, `FILES_FOUND: 4`

**Step 4: Categorize by Type (5.2)**
- `project_overview.md`: Has frontmatter with `type: project` → **project**
- `project_architecture_decisions.md`: Has frontmatter with `type: project` → **project**
- `project_pdf_war_stories.md`: No frontmatter, filename matches `*_war_stories*` → **project** (via 5.2.1 fallback)
- `project_debugging_playbook.md`: Has frontmatter with `type: project` → **project**

Update state:
```
FILES_BY_TYPE:
  - project: 4
  - reference: 0
  - user: 0
  - feedback: 0
CATEGORIZATION_METHODS:
  - frontmatter: 3
  - filename: 1
  - content inference: 0
```

**Step 5: Read and Cross-Reference (5.3)**

| File | New Info? | Enrichment Category | Target Section |
|------|-----------|---------------------|----------------|
| `project_overview.md` | Yes — adds business context not in code | Project context | Project Overview (inline) |
| `project_architecture_decisions.md` | Yes — 3 decisions with rationale | Architecture Decisions | Architecture Decisions (dedicated) |
| `project_pdf_war_stories.md` | Yes — 4 edge cases discovered in production | War Stories / Gotchas | Quirks & Gotchas (inline) |
| `project_debugging_playbook.md` | Yes — 5-step triage procedure | Debugging Playbook | Debugging Playbook (dedicated) |

Conflict check (5.3.1):
- `project_architecture_decisions.md` mentions "Redis for caching" but no Redis in codebase
- Resolution: Note as potential drift in Quirks & Gotchas

Update state: `FILES_READ: 4`, `FILES_CONTRIBUTING_NEW_INFO: 4`, `FILES_STALE_OR_CONTRADICTORY: 1`

**Step 6: Merge Plan (5.4)**
```
INLINE ENRICHMENTS:
- project_overview.md → Project Overview
  Content: Business context, processing goals, volume expectations
- project_pdf_war_stories.md → Quirks & Gotchas
  Content: 4 PDF parsing edge cases

NEW DEDICATED SECTIONS (3+ points each):
- Architecture Decisions
  Source: project_architecture_decisions.md
  Points: 3
  Summary: PostgreSQL choice, batch processing design, error handling strategy
- Debugging Playbook
  Source: project_debugging_playbook.md
  Points: 5
  Summary: Triage order for failed invoice processing

FOLDED INTO QUIRKS & GOTCHAS (<3 points):
- (none — war stories already going to Quirks)
```

**Step 7: Conditional Section Checklist (6.0)**

| Section | Decision | Reason |
|---------|----------|--------|
| Architecture Decisions | INCLUDE | 3 decisions documented |
| Pipeline / Data State | OMIT | No volume/state data in memories |
| Debugging Playbook | INCLUDE | 5 triage steps |
| Benign Warnings | OMIT | None documented |
| Memory Cross-Reference Summary | INCLUDE | Phase 5 executed |

**Step 8: Final State**
```
MEMORY_PATH: ~/.claude/projects/-Users-dev-projects-invoice-processor/memory/
MEMORY_INDEX_SOURCE: MEMORY.md
FILES_FOUND: 4
FILES_BY_TYPE:
  - project: 4
  - reference: 0
  - user: 0
  - feedback: 0
CATEGORIZATION_METHODS:
  - frontmatter: 3
  - filename: 1
  - content inference: 0
FILES_READ: 4
FILES_CONTRIBUTING_NEW_INFO: 4
FILES_STALE_OR_CONTRADICTORY: 1
MERGE_PLAN:
  - inline_enrichments: 2
  - dedicated_sections: [Architecture Decisions, Debugging Playbook]
  - folded_to_quirks: 0
CONDITIONAL_SECTIONS:
  - include: [Architecture Decisions, Debugging Playbook, Memory Cross-Reference Summary]
  - fold: []
  - omit: [Pipeline / Data State, Benign Warnings, Env Config, Domain Mappings, Attachment Handling, Shutdown / Resume, CLI Gotchas]
ESTIMATED_CONTRIBUTION: 35%
ESTIMATION_METHOD: heuristic (3-4 dedicated sections, moderate inline)
```

**Completion message excerpt:**
> Memory cross-reference: 4 memory files read, 4 contributed new information across 2 new sections and 2 enriched sections (~35% of output content from memory). Note: 1 file contained potentially stale information (Redis reference not found in code).

---

## Checklist Before Completing

Before writing the output file, verify:

- [ ] All 6 phases completed (Phase 5 may be skipped if no memory directory exists)
- [ ] No excluded files/folders were surveyed
- [ ] Module boundaries are clearly identified
- [ ] Quirks have specific file:line references where possible
- [ ] Infrastructure topology is accurately documented
- [ ] External services are identified with their purposes
- [ ] Testing setup is documented
- [ ] Git history provided useful insights (or noted if minimal history)
- [ ] Memory directory checked (exists or confirmed absent)
- [ ] If memories exist: all memory files read and cross-referenced against Phase 1-4 findings
- [ ] If memories exist: enrichments integrated into relevant sections (not dumped as a separate block)
- [ ] If memories exist: Memory Cross-Reference Summary section populated with counts
- [ ] Dedicated memory sections only included when 3+ bullet points of content exist
- [ ] If memories exist: Memory Health section populated with per-module coverage and recommendations

---

## After Survey Completion

Once you've written `.prompts/PROJECT_SURVEY_OUTPUT.md`, inform the user:

> **Survey complete.** I've written the comprehensive survey to `.prompts/PROJECT_SURVEY_OUTPUT.md`.
>
> The survey found [brief summary: e.g., "a brownfield Astro project with PostgreSQL and remote LLM inference"].
>
> **Memory cross-reference:** [N] memory files read, [M] contributed new information across [X] new sections and [Y] enriched sections (~[Z]% of output content from memory). [OR: "No project memory directory found — output based on codebase survey only."]
>
> Ready for the next step. Run the generators in order:
> 1. `"Read .prompts/CLAUDE_GENERATOR.md and create CLAUDE.md"`
> 2. `"Read .prompts/BOOTSTRAP_GENERATOR.md and create docs/BOOTSTRAP_PROMPT.md"`
> 3. `"Read .prompts/SESSION_GENERATOR.md and create docs/SESSION_HANDOFF.md"`
> 4. `"Read .prompts/EVOLUTION_GENERATOR.md and create docs/EVOLUTION.md"`
