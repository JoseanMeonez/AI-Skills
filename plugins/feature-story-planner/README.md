# feature-story-planner

Creates a feature plan as technical stories using Gherkin scenarios, then (after explicit approval) turns those stories into GitHub issues with:

- native issue relations (parent/sub-issue + blockers),
- semantic label alignment (including approved label creation),
- classic project board card placement.

## Includes

- `skills/feature-story-planner/SKILL.md`

## Typical flow

1. Gather required parameters (repo, epic, relations, board, label preferences).
2. Generate Markdown stories with Gherkin and relation metadata.
3. Ask for explicit approval.
4. Create issues, apply relations, align/create labels (with approval), and add cards.
