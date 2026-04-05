# Claude Code Documentation Generator

A system of prompt files that generate and maintain project documentation for Claude Code. Drop these files into any project, run them sequentially, and get comprehensive, consistent documentation that helps Claude understand your codebase.

Current stable release: [`v1.0`](Releases/v1.0).  
Repository metadata: [`CHANGELOG.md`](CHANGELOG.md), [`RELEASE_MANIFEST.md`](RELEASE_MANIFEST.md), [`VERSION`](VERSION).

---

## Quick Start

```bash
# 1. Copy latest release's .prompts/ folder to your project
cp -r Releases/v1.0/.prompts/ your-project/.prompts/

# 2. Add to .gitignore
echo ".prompts/" >> your-project/.gitignore

# 3. Run in Claude Code (sequential prompts)
"Read .prompts/PROJECT_SURVEY.md and follow its instructions"
"Read .prompts/CLAUDE_GENERATOR.md and create CLAUDE.md"
"Read .prompts/BOOTSTRAP_GENERATOR.md and create docs/BOOTSTRAP_PROMPT.md"
"Read .prompts/SESSION_GENERATOR.md and create docs/SESSION_HANDOFF.md"
"Read .prompts/EVOLUTION_GENERATOR.md and create docs/EVOLUTION.md"

# 4. Commit generated files, exit session
```

## Release Integrity

- `Releases/v1.0/.prompts/` is frozen as immutable release content.
- Verify prompt file checksums against [`RELEASE_MANIFEST.md`](RELEASE_MANIFEST.md) when needed.
- Each release contains a `.prompts/` folder — copy it directly into your project and gitignore it.

---

## What Gets Generated

| File | Location | Purpose |
|------|----------|---------|
| **CLAUDE.md** | Project root | Rules, commands, boundaries (<200 lines) |
| **BOOTSTRAP_PROMPT.md** | docs/ | Architecture, modules, quirks (100-200 lines) |
| **SESSION_HANDOFF.md** | docs/ | Current state, next steps (50-100 lines) |
| **EVOLUTION.md** | docs/ | Decisions and changes log (grows over time) |

---

## Files in Each Release

```
Releases/vX.X/
└── .prompts/
    ├── PROJECT_SURVEY.md           # Run first — surveys codebase
    ├── CLAUDE_GENERATOR.md         # Generates CLAUDE.md
    ├── BOOTSTRAP_GENERATOR.md      # Generates BOOTSTRAP_PROMPT.md
    ├── SESSION_GENERATOR.md        # Generates SESSION_HANDOFF.md
    ├── EVOLUTION_GENERATOR.md      # Generates EVOLUTION.md
    ├── CLAUDE_MAINTENANCE.md       # Updates CLAUDE.md
    ├── BOOTSTRAP_MAINTENANCE.md    # Updates BOOTSTRAP_PROMPT.md
    ├── SESSION_MAINTENANCE.md      # Updates SESSION_HANDOFF.md
    └── EVOLUTION_MAINTENANCE.md    # Updates EVOLUTION.md
```

---

## Workflows

### Initial Setup (New or Existing Project)

```
1. Copy .prompts/ folder from latest release → your-project/.prompts/
2. Add .prompts/ to .gitignore
3. Run PROJECT_SURVEY.md (expensive, one-time)
4. Run each generator sequentially (same session)
5. Review and commit 4 generated files
6. Exit/clear session
```

### New Session (Daily Use)

```
1. Claude Code auto-reads CLAUDE.md
2. Paste docs/BOOTSTRAP_PROMPT.md
3. Paste docs/EVOLUTION.md  
4. Paste docs/SESSION_HANDOFF.md
5. Work...
```

### Before Commits (Maintenance)

```
# Run whichever apply:
"Read .prompts/SESSION_MAINTENANCE.md and update docs/SESSION_HANDOFF.md"
"Read .prompts/CLAUDE_MAINTENANCE.md and update CLAUDE.md"
"Read .prompts/BOOTSTRAP_MAINTENANCE.md and update docs/BOOTSTRAP_PROMPT.md"
"Read .prompts/EVOLUTION_MAINTENANCE.md and update docs/EVOLUTION.md"
```

---

## Release History

### v1.0 — 2026-04-03

**Initial release.** Complete documentation generation system.

**Generators:**
- PROJECT_SURVEY.md — 5-phase codebase survey with exclusion rules
- CLAUDE_GENERATOR.md — Lean rules file (<200 lines) with commands, boundaries, gotchas
- BOOTSTRAP_GENERATOR.md — Deep onboarding doc (100-200 lines) with module boundaries, quirks, reading list
- SESSION_GENERATOR.md — State snapshot (50-100 lines) with status, next steps, blockers
- EVOLUTION_GENERATOR.md — Decision/change log seeded from git history

**Maintenance:**
- CLAUDE_MAINTENANCE.md — Detects command, boundary, gotcha changes
- BOOTSTRAP_MAINTENANCE.md — Detects module, quirk, architecture changes
- SESSION_MAINTENANCE.md — Updates status, uncommitted changes, next steps
- EVOLUTION_MAINTENANCE.md — Appends decisions, features, bugfixes

**Features:**
- Reverse chronological EVOLUTION.md (LLM-friendly)
- File:line references for quirks
- ASCII infrastructure diagrams for multi-machine setups
- `_old` backup convention before updates
- Cross-file suggestions (maintenance prompts suggest related updates)
- Decision Index with IDs (D-001, D-002)
- Ready-to-commit vs WIP distinction in SESSION_HANDOFF

**Examples included:**
- Single-machine Python CLI
- Multi-machine Astro + LLM (Mac + RTX 3090)
- Browser automation with macOS A11y + Chrome extension
- Full-stack tax pipeline with Docker, PostgreSQL, local LLM

---

## Versioning

- **Major (X.0):** Breaking changes to file format or workflow
- **Minor (X.Y):** New features, improved examples, non-breaking enhancements

---

## Per-Project Structure

After running generators:

```
your-project/
├── CLAUDE.md                        # ✓ Committed
├── docs/
│   ├── BOOTSTRAP_PROMPT.md          # ✓ Committed
│   ├── SESSION_HANDOFF.md           # ✓ Committed
│   └── EVOLUTION.md                 # ✓ Committed
├── .prompts/                        # ✗ Gitignored (copied from release)
│   ├── PROJECT_SURVEY.md
│   ├── PROJECT_SURVEY_OUTPUT.md     # Generated by survey
│   ├── CLAUDE_GENERATOR.md
│   ├── BOOTSTRAP_GENERATOR.md
│   ├── SESSION_GENERATOR.md
│   ├── EVOLUTION_GENERATOR.md
│   ├── CLAUDE_MAINTENANCE.md
│   ├── BOOTSTRAP_MAINTENANCE.md
│   ├── SESSION_MAINTENANCE.md
│   └── EVOLUTION_MAINTENANCE.md
└── .gitignore                       # Contains: .prompts/
```

---

## Tips

### Keep CLAUDE.md Lean
- Under 200 lines
- Commands, rules, gotchas only
- Deep explanations → BOOTSTRAP_PROMPT.md

### Quirks Need File:Line References
```markdown
# Good
3. **Qwen 3 thinks by default** (`src/llm/client.ts:47`) — use /no_think

# Bad  
3. **LLM sometimes adds extra text** — be careful
```

### Update SESSION_HANDOFF.md Frequently
- End of every session
- Before every commit
- It's the "where are we now" snapshot

### Migrate Decisions to EVOLUTION.md
- SESSION_HANDOFF.md captures decisions temporarily
- Run EVOLUTION_MAINTENANCE to make them permanent
- Prevents decisions from being lost

### Multi-Machine? Document It
- Infrastructure diagrams in BOOTSTRAP_PROMPT.md
- Machine context table in CLAUDE.md
- IP addresses, ports, roles

---

## Future Roadmap

Potential future enhancements:

- [ ] Automated drift detection (docs vs code)
- [ ] Integration with Claude Code slash commands
- [ ] Project-type templates (Python, Node, Rust, Swift)
- [ ] CI/CD integration for doc validation
- [ ] Diff view for maintenance updates

---

## License

Licensed under the [Simple Public License 2.0 (SimPL-2.0)](LICENSE).

---

## Acknowledgments

Developed through iterative conversation, drawing patterns from:
- Browser Automation System (macOS A11y + Chrome MV3)
- Tax Pipeline (Astro + local LLM + PostgreSQL)
- Multiple Claude Code projects with varying complexity

Built for single-developer workflows with multi-machine setups, heavy Docker usage, and local LLM inference.
