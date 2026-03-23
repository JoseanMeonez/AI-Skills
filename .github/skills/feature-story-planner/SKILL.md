---
name: feature-story-planner
description: Build a feature plan split into technical stories with Gherkin scenarios in Markdown. Collect required parameters, ask for missing ones, and after explicit approval create one GitHub issue per story, apply native parent/blocker relations, and add each issue to a classic project board using GitHub CLI.
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
11. `parent_relation_mode` - how parent linkage is modeled (`epic-parent` or `story-parent`).
12. `dependency_mode` - dependency semantics to apply (`blocks` / `blocked_by`).
13. `relation_overrides` - optional per-story relation overrides (`none` if not used).

## Missing-parameter behavior

If any required parameter is missing:

- Ask for exactly one missing parameter at a time.
- Do not generate final stories or execute GitHub CLI commands until all required parameters are present.
- If `project_column_id` is unknown, ask the user to provide it (for classic boards this is required for card creation).
- If relation parameters are ambiguous, ask for explicit direction before proceeding.

## Story output format (Markdown first)

After all parameters are known, output a Markdown plan with multiple stories. Match this style:

~~~markdown
## Story <epic>.<n> — <Story title>
**Epic:** <epic id> - <epic title>
**Category:** <target category>
**Files:** `<file1>`, `<file2>`
**Relations:** Parent: <epic|story-key|none>; Blocks: <story keys|none>; Blocked by: <story keys|none>

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
- Include explicit relation metadata for every story.

## Approval gate (mandatory)

After presenting the stories, stop and ask for explicit approval.

Use a direct question such as:

`Do you approve creating these stories as GitHub issues, applying parent/blocker relations, and adding them to the classic project column now?`

Do not create issues, relations, or project cards until explicit approval is received.

## Post-approval execution flow (GitHub CLI + GitHub GraphQL API)

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

2. Capture issue URL and number; resolve both numeric ID and node ID:

```bash
gh api repos/OWNER/REPO/issues/<issue_number> --jq '{id: .id, node_id: .node_id}'
```

3. Apply parent/sub-issue relations natively (when configured):

```bash
gh api graphql -f query='
mutation($issueId: ID!, $subIssueId: ID!) {
  addSubIssue(input: {issueId: $issueId, subIssueId: $subIssueId}) {
    issue { id number }
    subIssue { id number }
  }
}' -F issueId=<PARENT_NODE_ID> -F subIssueId=<CHILD_NODE_ID>
```

4. Apply blocker relations natively (when configured):

```bash
gh api graphql -f query='
mutation($issueId: ID!, $blockingIssueId: ID!) {
  addBlockedBy(input: {issueId: $issueId, blockingIssueId: $blockingIssueId}) {
    issue { id number }
    blockingIssue { id number }
  }
}' -F issueId=<BLOCKED_NODE_ID> -F blockingIssueId=<BLOCKER_NODE_ID>
```

5. Resolve numeric issue `id` and add card to classic project column:

```bash
gh api repos/OWNER/REPO/issues/<issue_number> --jq '.id'
```

```bash
gh api \
  --method POST \
  -H "Accept: application/vnd.github+json" \
  /projects/columns/<project_column_id>/cards \
  -f content_id=<issue_id> \
  -f content_type=Issue
```

6. Report:
   - created issue list,
   - applied parent/sub-issue and blocker relations,
   - confirmation each card was added to the column,
   - any failures and what input is needed to retry.

## Reliability constraints

- Never fabricate IDs, URLs, or command results.
- Validate all relation targets exist before linking.
- If any CLI/API command fails, show the exact error and request the missing/fixed parameter.
- If a matching story issue already exists, ask whether to skip, update, or create a new issue.
