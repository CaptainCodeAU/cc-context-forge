# Changelog

All notable changes to cc-context-forge are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/).

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
