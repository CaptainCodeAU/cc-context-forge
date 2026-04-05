# Changelog

All notable changes to cc-context-forge are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/).

## [2.2] — 2026-04-05

### Changed

- CLAUDE_GENERATOR.md: Replaced fixed 200-line cap with Content Allocation Guide (Simple 40-80, Medium 80-150, Complex 150-250 lines) based on 3-axis complexity scoring
- CLAUDE_GENERATOR.md: Added cardinal rule — session must be self-sufficient from CLAUDE.md alone
- CLAUDE_GENERATOR.md: Added Cross-Reference Guide mapping 14 content types to correct files
- CLAUDE_GENERATOR.md: Expanded examples to 3 tiers (25/90/220 lines) with full env var, infrastructure, and project structure coverage
- CLAUDE_MAINTENANCE.md: Expanded detection from 6 to 10 areas (added env vars, patterns, domain conventions, project structure)
- CLAUDE_MAINTENANCE.md: Added Anti-Degradation Safeguards (6 rules) and Cross-Reference Guide
- BOOTSTRAP_GENERATOR.md: Added Content Allocation Guide with BOOTSTRAP-specific axes (modules, pipeline stages, infrastructure)
- BOOTSTRAP_GENERATOR.md: Added API-reference quality mandate — Key Files must list 2-5 exports by name
- BOOTSTRAP_GENERATOR.md: Added quirk completeness requirement — list ALL bugs, not representative samples
- BOOTSTRAP_GENERATOR.md: Added Anti-Patterns section (8 patterns to avoid) and Cross-Reference Guide
- BOOTSTRAP_GENERATOR.md: Added complex project example (~220 lines) with 17 categorized quirks and 25+ key files
- BOOTSTRAP_MAINTENANCE.md: Expanded detection from 7 to 9 areas (added module coverage gap audit, export change detection)
- BOOTSTRAP_MAINTENANCE.md: Added Anti-Degradation Safeguards (9 rules including library module coverage)
- SESSION_GENERATOR.md: Added test file mapping column for pipeline projects and mandatory test summary line
- SESSION_GENERATOR.md: Added per-file annotation requirement for uncommitted changes
- SESSION_GENERATOR.md: Added strategic-first next steps (Option A/B/C before procedural tasks)
- SESSION_GENERATOR.md: Added resolved-issue tracking subsection with commit hashes
- SESSION_MAINTENANCE.md: Added Test Changes detection area and test file mapping preservation
- SESSION_MAINTENANCE.md: Added strategic option carry-forward and resolved issue tracking
- EVOLUTION_GENERATOR.md: Added companion docs header establishing backward/forward-looking ownership with SESSION_HANDOFF
- EVOLUTION_GENERATOR.md: Added operational workflow decisions as a Decision type
- EVOLUTION_GENERATOR.md: Added bidirectional cross-referencing between entries and milestone completeness requirement
- EVOLUTION_GENERATOR.md: Added file reference verification with retained/abandoned/moved taxonomy
- EVOLUTION_MAINTENANCE.md: Added SESSION_HANDOFF absorption workflow with cross-reference replacement
- EVOLUTION_MAINTENANCE.md: Added cross-referencing, file reference verification, and milestone completeness sections
- Version bump to 2.2 across all 9 prompt files

## [2.1] — 2026-04-05

### Added

- PROJECT_SURVEY.md: Section 5.7 — Memory Health Assessment evaluates per-module memory coverage, recommends specific memories to create (with suggested filenames), and identifies stale memories to update
- PROJECT_SURVEY.md: Memory Health output section with module coverage table, recommended memories, and stale memory list
- PROJECT_SURVEY.md: Memory Health row in conditional section checklist (6.0)

## [2.0] — 2026-04-05

### Added

- PROJECT_SURVEY.md: Fallback memory file discovery via filename patterns when YAML frontmatter is missing (5.2.1)
- PROJECT_SURVEY.md: Common memory filename hints for fallback directory scanning (5.1)
- PROJECT_SURVEY.md: Conflict resolution rules for memory vs codebase contradictions (5.3.1)
- PROJECT_SURVEY.md: Explicit merge plan format with inline/dedicated/folded categories (5.4)
- PROJECT_SURVEY.md: Memory coverage assessment with sparse/stale/comprehensive criteria (5.6)
- PROJECT_SURVEY.md: Percentage estimation guidance with precise and heuristic methods (5.5)
- PROJECT_SURVEY.md: Conditional section checklist evaluated before writing output (6.0)
- PROJECT_SURVEY.md: Phase 5 early exit check with prominent skip path
- PROJECT_SURVEY.md: Phase 5 state tracking template maintained throughout execution
- PROJECT_SURVEY.md: Example 5a — complete memory processing trace

### Changed

- PROJECT_SURVEY.md: YAML frontmatter handling now covers all scenarios (valid, missing type, malformed, missing) via decision tree (5.2)
- PROJECT_SURVEY.md: Enrichment categories table gains "Target Section" column for direct mapping (5.3)
- Version bump to 2.0 — significant structural improvements to Phase 5 methodology

## [1.1] — 2026-04-05

### Added

- PROJECT_SURVEY.md: Phase 5 — Memory Cross-Reference that reads Claude Code's project memory (`~/.claude/projects/.../memory/`) and enriches the survey output with knowledge not derivable from code alone (architecture decisions, war stories, operational workflows, data state, debugging playbooks)
- 11 conditional memory-enriched sections in the survey output template
- New Example 5 demonstrating memory-enriched survey output
- 5 memory-specific items in the completion checklist

### Changed

- PROJECT_SURVEY.md: Expanded from 5 phases to 6 phases (new Phase 5: Memory Cross-Reference, old Phase 5 renumbered to Phase 6: Output)
- All prompt files carry `<!-- Version: 1.1 -->` header

## [1.0] — 2026-04-03

### Changed

- Moved prompt files into `.prompts/` subfolder within each release (`Releases/v1.0/.prompts/`) so users can copy the folder directly to their project without guesswork

### Added

- Complete documentation generation system: 5 generators + 4 maintenance prompts
- PROJECT_SURVEY.md — 5-phase codebase survey with exclusion rules
- CLAUDE_GENERATOR.md — Lean rules file (<200 lines) with commands, boundaries, gotchas
- BOOTSTRAP_GENERATOR.md — Deep onboarding doc (100-200 lines) with module boundaries and quirks
- SESSION_GENERATOR.md — State snapshot (50-100 lines) with status and next steps
- EVOLUTION_GENERATOR.md — Decision/change log seeded from git history
- Matching maintenance prompts for all four generated documents
- Reverse chronological EVOLUTION.md format (LLM-friendly)
- File:line references for quirks
- ASCII infrastructure diagrams for multi-machine setups
- `_old` backup convention before updates
- Cross-file suggestions in maintenance prompts
- Decision Index with IDs (D-001, D-002, ...)
- Ready-to-commit vs WIP distinction in SESSION_HANDOFF
- Examples for Python CLI, multi-machine Astro+LLM, browser automation, and full-stack tax pipeline
