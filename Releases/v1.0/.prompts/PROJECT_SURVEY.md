# PROJECT_SURVEY.md
<!-- Version: 1.0 -->

> **What this file does:** Thoroughly surveys an entire codebase to build comprehensive understanding before generating documentation. Run this ONCE at the start — it's expensive but creates the foundation for all subsequent generators.
>
> **How to run:** Prompt Claude Code with: `"Read .prompts/PROJECT_SURVEY.md and follow its instructions"`
>
> **Output:** Creates `.prompts/PROJECT_SURVEY_OUTPUT.md` and keeps the survey in context for subsequent generators.

---

## Instructions for Claude Code

You are about to survey this project thoroughly. This will consume significant context, but the goal is to understand the project deeply enough that the generated documentation (CLAUDE.md, BOOTSTRAP_PROMPT.md, SESSION_HANDOFF.md, EVOLUTION.md) will be accurate and comprehensive.

**Work through each phase sequentially. Do not skip phases.**

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

## Phase 5: Output

### 5.1 Write Survey Output
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

## Recommendations
- [Any immediate observations about documentation gaps]
- [Suggestions for CLAUDE.md content]
- [Suggestions for BOOTSTRAP_PROMPT.md content]
```

### 5.2 Keep in Context
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

## Checklist Before Completing

Before writing the output file, verify:

- [ ] All 5 phases completed
- [ ] No excluded files/folders were surveyed
- [ ] Module boundaries are clearly identified
- [ ] Quirks have specific file:line references where possible
- [ ] Infrastructure topology is accurately documented
- [ ] External services are identified with their purposes
- [ ] Testing setup is documented
- [ ] Git history provided useful insights (or noted if minimal history)

---

## After Survey Completion

Once you've written `.prompts/PROJECT_SURVEY_OUTPUT.md`, inform the user:

> **Survey complete.** I've written the comprehensive survey to `.prompts/PROJECT_SURVEY_OUTPUT.md`.
>
> The survey found [brief summary: e.g., "a brownfield Astro project with PostgreSQL and remote LLM inference"].
>
> Ready for the next step. Run the generators in order:
> 1. `"Read .prompts/CLAUDE_GENERATOR.md and create CLAUDE.md"`
> 2. `"Read .prompts/BOOTSTRAP_GENERATOR.md and create docs/BOOTSTRAP_PROMPT.md"`
> 3. `"Read .prompts/SESSION_GENERATOR.md and create docs/SESSION_HANDOFF.md"`
> 4. `"Read .prompts/EVOLUTION_GENERATOR.md and create docs/EVOLUTION.md"`
