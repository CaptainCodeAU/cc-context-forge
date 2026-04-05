# Changelog

All notable changes to cc-context-forge are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/).

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
