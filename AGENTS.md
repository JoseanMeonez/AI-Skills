# Repository Instructions (Canonical)

This repository stores custom skills and custom agent definitions.

## Scope

- Keep this repository tool-agnostic across Copilot CLI, Claude Code, Gemini CLI, and similar assistants.
- Prefer shared guidance here, and keep tool-specific files as thin wrappers.

## Build, test, and lint

No build, lint, or automated test commands are currently defined.

If tooling is added later, document:

- full-suite command,
- single-test command (file-scoped or test-name scoped).

## Architecture

- Repository is configuration-first: instructions, skills, and agent profiles.
- Keep skill and agent units self-contained, with local resources in each unit directory.
- Separate canonical shared guidance from tool-specific entry-point files.

## Conventions

- Do not invent commands, workflows, IDs, or validation steps; use repository-backed facts only.
- Keep semantic consistency across assistant-specific files even when syntax differs.
- When adding or changing guidance, update this file first, then update wrappers that point to it.
