# PROJECT_SURVEY.md
<!-- Version: 1.1 -->

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

This phase captures institutional knowledge that code analysis alone cannot reveal: *why* things are the way they are, *what went wrong* getting there, and *how to operate* the system. In real-world testing, this phase added ~30-50% more content to the survey output.

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

### 5.2 Categorize by Type
Read each memory file's YAML frontmatter (first 5 lines). Group by `type` field:

| Type | Contains | Survey Relevance |
|------|----------|-----------------|
| `project` | Architecture decisions, war stories, workflows, data state, quirks | Highest value — directly enriches survey with rationale and operational knowledge |
| `reference` | Documentation structure, external resources, conventions | Enriches Existing Documentation and Recommendations sections |
| `user` | Project owner info, environment, security preferences | Enriches Infrastructure and Project Overview sections |
| `feedback` | User preferences for workflow, commits, tooling | Enriches Recommendations section |

**Read `project` type memories first** — they contain ~80% of the unique value.

### 5.3 Read and Cross-Reference Each Memory
For each memory file (prioritised by type: project → reference → user → feedback):

1. **Read the full content** (not just frontmatter)
2. **Compare against Phases 1-4 findings** — identify what this memory adds that the codebase survey alone didn't capture
3. **Classify each enrichment** into one or more of these categories:

| Enrichment Category | What to Look For in Memories |
|---------------------|------------------------------|
| Architecture Decisions | Design rationale with "why" explanations, trade-offs, constraints, rejected alternatives |
| War Stories / Gotchas | Bugs found in production, cascading failures, defensive patterns explained by past incidents |
| Operational Workflows | Multi-step processes (e.g., iterative rule proposal cycles), priority strategies, command sequences |
| Pipeline / Data State | Current processing volumes, per-component breakdowns, status percentages, run IDs |
| Domain-Specific Mappings | Virtual fields, view vs table distinctions, naming translations, field aliases |
| Debugging Playbook | Triage order, known causes per component, diagnostic commands, "check this first" lists |
| Benign Warnings | Expected console output that looks alarming but is handled gracefully in code |
| Shutdown / Resume | Graceful stop patterns (SIGINT handling), state persistence, rollback procedures, `--resume` flags |
| Env Config Details | Configuration hierarchy, per-user/per-component structure, secret management patterns |
| Attachment / File Handling | Size limits, dedup strategies, filters, storage paths, extraction rules |
| CLI Patterns | Flag parsing quirks, quoting issues, command workarounds, ad-hoc patterns across scripts |
| Test Insights | Skip conditions for external deps, factory patterns, environment-dependent behaviour |

4. **Note which existing survey section** each enrichment extends, or whether it requires a new dedicated section

### 5.4 Merge Plan
Before writing output, create a merge plan that determines where each enrichment goes:

**Inline enrichments** (add to existing Phase 1-4 survey sections):
- User info → enrich Infrastructure section with owner context
- Test insights → enrich Testing section with skip conditions and patterns
- Reference docs → enrich Existing Documentation with coverage notes
- Architecture decisions → enrich Module Boundaries with rationale

**New dedicated sections** (add when memories provide sufficient content):
- Only create a dedicated section if **3 or more bullet points** of content exist
- If fewer than 3 points, fold the content into the Quirks & Gotchas section instead
- This prevents sparse memories from generating stub sections

**Also enrich the Quirks & Gotchas list** — war stories and CLI gotchas from memory often surface quirks that code analysis missed. Add these with file:line references where available.

### 5.5 Quantify Memory Contribution
Count and record:
- Total memory files read
- Memory files that contributed new information (not already captured in Phases 1-4)
- Number of new dedicated sections to be added
- Number of existing sections to be enriched
- Estimated percentage of total output content that comes from memory vs codebase survey

These counts will be included in the Memory Cross-Reference Summary section and reported in the completion message.

---

## Phase 6: Output

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
<!-- CONDITIONAL: Include only if project memories contain design rationale. Omit for projects without memory or without architecture decisions. -->
| Decision | Rationale |
|----------|-----------|
| ... | Why this choice was made, what alternatives were rejected |

## Pipeline / Data State
<!-- CONDITIONAL: Include only if project memories contain processing volumes, status breakdowns, or current pipeline state. Omit for new projects. -->
- **Total records/items:** [count]
- **Per-component breakdown:** [table if applicable]
- **Current processing status:** [what's done, what remains]

## Operational Workflows
<!-- CONDITIONAL: Include only if project memories describe multi-step iterative processes. -->
**[Workflow name]:**
1. [Step 1]
2. [Step 2]
...
- **Results:** [what this workflow achieved]
- **Priority/ordering strategy:** [if applicable]

## Debugging Playbook
<!-- CONDITIONAL: Include only if project memories contain debugging triage procedures. List in recommended triage order. -->
1. **[First check]** — [diagnostic command]. Common causes: ...
2. **[Second check]** — ...

## Benign Warnings
<!-- CONDITIONAL: Include only if project memories list expected warnings/errors that are NOT bugs. -->
- `[warning text pattern]` — [why it's benign and how it's handled]

## Env Config Hierarchy
<!-- CONDITIONAL: Include only if project memories describe a complex or non-obvious env var structure. -->
[Tree or table showing configuration hierarchy]

## Domain-Specific Mappings
<!-- CONDITIONAL: Include only if project memories document field name translations, virtual fields, or view vs table distinctions. -->
| Public Name | Maps To | Notes |
|-------------|---------|-------|
| ... | ... | ... |

## Attachment / File Handling
<!-- CONDITIONAL: Include only if project memories describe file processing limits, dedup strategies, or storage paths. -->
- **Max size:** ...
- **Dedup strategy:** ...
- **Storage path pattern:** ...

## Shutdown / Resume Patterns
<!-- CONDITIONAL: Include only if project memories describe graceful shutdown or resume-from-failure patterns. -->
- **[Component]:** [how it handles interruption and resume]

## CLI Gotchas
<!-- CONDITIONAL: Include only if project memories document CLI-specific issues not already in Quirks & Gotchas. -->
- **[Script/command]:** [the gotcha and workaround]

## Memory Cross-Reference Summary
<!-- ALWAYS include this section when Phase 5 was executed. Omit only if Phase 5 was skipped (no memory directory). -->
- **Memory files found:** [N]
- **Files contributing new information:** [M] of [N]
- **New sections added from memory:** [list of section names]
- **Existing sections enriched:** [list of section names]
- **Estimated memory contribution:** ~[X]% of total output content

## Recommendations
- [Any immediate observations about documentation gaps]
- [Suggestions for CLAUDE.md content]
- [Suggestions for BOOTSTRAP_PROMPT.md content]
- **Memory coverage:** [sparse/stale/comprehensive — note areas where memories should be created]
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
