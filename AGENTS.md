# Repository Instructions (Canonical)

This repository is a custom plugin marketplace (Claude format primary) that packages reusable AI workflows.

## Scope

- Keep this repository tool-agnostic across Copilot CLI, Claude Code, Gemini CLI, and similar assistants.
- Prefer shared guidance here, and keep tool-specific files as thin wrappers.

## Build, test, and lint

No build, lint, or automated test commands are currently defined.

If tooling is added later, document:

- full-suite command,
- single-test command (file-scoped or test-name scoped).

## Architecture

- Repository is configuration-first: marketplace manifest, plugin packages, and instruction wrappers.
- Marketplace registry lives at `.claude-plugin/marketplace.json`.
- Installable plugins live under `plugins/<plugin-name>/`.
- Each plugin should be self-contained (`.claude-plugin/plugin.json`, `skills/`, optional `commands/`, plugin `README.md`).
- Keep canonical shared guidance separate from tool-specific entry-point wrapper files.

## Skill locations

- Marketplace plugin skills: `plugins/<plugin-name>/skills/<skill-name>/SKILL.md`
- Legacy direct skill paths (`.github/skills` / `.claude/skills`) should be avoided for new additions in favor of plugin packaging.

## Conventions

- Do not invent commands, workflows, IDs, or validation steps; use repository-backed facts only.
- Keep semantic consistency across assistant-specific files even when syntax differs.
- When adding or changing guidance, update this file first, then update wrappers that point to it.
