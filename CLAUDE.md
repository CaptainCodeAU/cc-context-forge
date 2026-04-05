# CLAUDE.md — cc-context-forge

Prompt-file system that generates and maintains documentation for Claude Code projects.
No build step, no runtime code — the product is 9 markdown prompt files per release.

## Repository Structure

| Path | Purpose |
|------|---------|
| `Releases/v2.0/.prompts/` | Latest release — v2.0 prompt files (immutable) |
| `Releases/v1.1/.prompts/` | Frozen v1.1 prompt files (immutable) |
| `Releases/v1.0/.prompts/` | Frozen v1.0 prompt files (immutable) |
| `README.md` | Project README |
| `CHANGELOG.md` | Version history (Keep a Changelog format) |
| `RELEASE_MANIFEST.md` | SHA-256 checksums for release files |
| `VERSION` | Current version number (bare, no `v` prefix) |

## Key Rules

1. **Never modify files in released `Releases/vX.Y/.prompts/` directories** — released content is immutable
2. **Version format** — `VERSION` contains bare number (`1.0`). Directory names use `v` prefix (`v1.0/`). Prompt files use `<!-- Version: 1.0 -->` on line 2.
3. **New releases** go in `Releases/vX.Y/.prompts/` directories. Update CHANGELOG.md, RELEASE_MANIFEST.md, VERSION, and README.md.
4. **License is SimPL 2.0** (GPL-2.0 equivalent, open source)

## File Naming Conventions

- Generators: `{NAME}_GENERATOR.md` — creates a document from scratch
- Maintenance: `{NAME}_MAINTENANCE.md` — updates an existing document
- Prompt files use `UPPER_SNAKE_CASE.md`

## Workflow for New Releases

1. Create `Releases/vX.Y/.prompts/` directory
2. Copy/modify prompt files into it
3. Update `<!-- Version: X.Y -->` in each prompt file
4. Generate checksums: `shasum -a 256 Releases/vX.Y/.prompts/*.md`
5. Update RELEASE_MANIFEST.md, CHANGELOG.md, VERSION
6. Update release link in README.md
