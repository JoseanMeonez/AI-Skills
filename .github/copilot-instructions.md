# Repository Instructions (Skills and Agents)

## Build, test, and lint
No build, lint, or automated test commands are currently defined in this repository.

If tooling is added later, document both of these in this file:
- the full-suite command,
- the single-test command (file-scoped or test-name scoped).

## High-level architecture
This repository is configuration-first: it is meant to store custom skill and custom agent definitions rather than application runtime code.

Keep the structure assistant-agnostic by separating:
- shared guidance (cross-assistant conventions and canonical behavior),
- assistant-specific instruction surfaces (for example `.github/copilot-instructions.md`, `AGENTS.md`, `CLAUDE.md`, `.cursorrules`, `.cursor/rules/` when present),
- skill/agent assets grouped by unit so each skill/agent can be updated independently.

When behavior changes, update shared guidance first, then align assistant-specific files that exist in the repository.

## Key conventions
- Keep guidance neutral across Copilot CLI, Claude Code, Gemini CLI, and similar tools unless a file is explicitly assistant-specific.
- Do not invent commands, workflows, or validation steps; only document what is present in repo files.
- Keep each skill/agent self-contained (instructions plus any local assets/templates/scripts needed by that unit).
- Preserve semantic consistency across assistant config files, even if each assistant uses different file names or syntax.
- Use this file as the Copilot entry point and keep references to other assistant configs accurate as they are added.
