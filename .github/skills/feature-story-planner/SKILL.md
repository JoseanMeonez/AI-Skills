---
name: feature-story-planner
description: Build a feature plan split into technical stories with Gherkin scenarios in Markdown. Collect required parameters, ask for missing ones, and after explicit approval create one GitHub issue per story and add each issue to a classic project board using GitHub CLI.
---

# Feature Story Planner Skill

Use this skill when the user wants a feature decomposition into implementable technical stories with acceptance scenarios.

## Required parameters

Collect all required parameters before writing the final story set:

1. `feature_summary` - concise feature scope.
2. `repository` - target repo in `OWNER/REPO` format.
3. `epic_id` - epic identifier (for example `1`).
4. `epic_title` - epic title.
5. `target_category` - category/layer (`Frontend`, `Backend`, `Platform`, etc.).
6. `story_granularity` - expected story size (`small`, `medium`, `large`).
7. `project_column_id` - classic project column ID where cards must be added.
8. `labels` - issue labels to apply (`none` if not used).
9. `assignee` - GitHub login (`none` if not used).
10. `milestone` - milestone number (`none` if not used).

## Missing-parameter behavior

If any required parameter is missing:

- Ask for exactly one missing parameter at a time.
- Do not generate final stories or execute GitHub CLI commands until all required parameters are present.
- If `project_column_id` is unknown, ask the user to provide it (for classic boards this is required for card creation).

## Story output format (Markdown first)

After all parameters are known, output a Markdown plan with multiple stories. Match this style:

~~~markdown
## Story <epic>.<n> — <Story title>
**Epic:** <epic id> - <epic title>
**Category:** <target category>
**Files:** `<file1>`, `<file2>`

---

### Feature: <feature slice name>
> As a <role>, I want <capability> so that <outcome>.

#### Task 1: <task title>
```gherkin
Feature: <task feature name>
Scenario: <scenario title>
Given ...
When ...
Then ...
And ...
```

#### Task 2: <task title>
```gherkin
Feature: <task feature name>
Scenario: <scenario title>
Given ...
When ...
Then ...
```
~~~

Rules:

- Use concrete technical detail and implementation-oriented scenarios.
- Include at least one Gherkin block per task.
- Keep each story aligned with `story_granularity`.
- Keep the output in Markdown.

## Approval gate (mandatory)

After presenting the stories, stop and ask for explicit approval.

Use a direct question such as:

`Do you approve creating these stories as GitHub issues and adding them to the classic project column now?`

Do not create issues or project cards until explicit approval is received.

## Post-approval execution flow (GitHub CLI)

After explicit approval:

1. Create one issue per story:

```bash
gh issue create \
  --repo OWNER/REPO \
  --title "Story <epic>.<n> — <Story title>" \
  --body-file <story_markdown_file> \
  [--label "<label1>" --label "<label2>"] \
  [--assignee "<assignee>"] \
  [--milestone "<milestone_number>"]
```

2. Capture the issue URL from command output and extract the issue number.

3. Resolve the numeric issue `id` needed by classic project cards:

```bash
gh api repos/OWNER/REPO/issues/<issue_number> --jq '.id'
```

4. Add the issue to the classic project column:

```bash
gh api \
  --method POST \
  -H "Accept: application/vnd.github+json" \
  /projects/columns/<project_column_id>/cards \
  -f content_id=<issue_id> \
  -f content_type=Issue
```

5. Report:
   - created issue list,
   - confirmation each card was added to the column,
   - any failures and what input is needed to retry.

## Reliability constraints

- Never fabricate IDs, URLs, or command results.
- If any CLI command fails, show the exact error and request the missing/fixed parameter.
- If a matching story issue already exists, ask whether to skip, update, or create a new issue.
