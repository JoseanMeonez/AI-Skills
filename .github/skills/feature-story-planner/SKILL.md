---
name: feature-story-planner
description: Build a feature plan split into technical stories with Gherkin scenarios in Markdown. Ask whether to deliver to GitHub Issues or Notion, collect required parameters, and after explicit approval create one item per story with parent/blocker relations, aligned labels, and board placement — using GitHub CLI for GitHub or Notion MCP tools for Notion.
---

# Feature Story Planner Skill

Use this skill when the user wants a feature decomposition into implementable technical stories with acceptance scenarios.

## Step 0 — Choose destination

Before collecting any other parameters, ask:

> Where should the stories be created? Reply `github` for GitHub Issues or `notion` for a new Notion Kanban board.

Do not proceed until `destination` is explicitly set to `github` or `notion`.

## Required parameters

Collect all required parameters before writing the final story set. Ask for exactly one missing parameter at a time.

### Shared parameters (both destinations)

1. `feature_summary` — concise feature scope.
2. `epic_id` — epic identifier (for example `1`).
3. `epic_title` — epic title.
4. `target_category` — category/layer (`Frontend`, `Backend`, `Platform`, etc.).
5. `story_granularity` — expected story size (`small`, `medium`, `large`).
6. `labels` — preferred label hints (`none` if no preference).
7. `assignee` — GitHub login (GitHub) or user email (Notion) (`none` if not used).
8. `milestone` — milestone number (GitHub) or sprint name (Notion) (`none` if not used).
9. `parent_relation_mode` — how parent linkage is modeled (`epic-parent` or `story-parent`).
10. `dependency_mode` — dependency semantics to apply (`blocks` / `blocked_by`).
11. `relation_overrides` — optional per-story relation overrides (`none` if not used).

### GitHub-only parameters

12. `repository` — target repo in `OWNER/REPO` format.
13. `project_column_id` — classic project column ID where cards must be added.

### Notion-only parameters

12. `notion_parent_page_id` — Notion page ID (with or without dashes) of the page where the new Kanban database will be created. Ask the user to share the page URL or ID.

## Missing-parameter behavior

- Ask for exactly one missing parameter at a time.
- Do not generate final stories or execute any commands or tools until all required parameters are present.
- If `project_column_id` (GitHub) or `notion_parent_page_id` (Notion) is unknown, prompt the user to provide it.
- If relation parameters are ambiguous, ask for explicit direction before proceeding.

## Story output format (Markdown first)

After all parameters are known, output a Markdown plan with multiple stories. Match this style:

~~~markdown
## Story <epic>.<n> — <Story title>
**Epic:** <epic id> - <epic title>
**Category:** <target category>
**Files:** `<file1>`, `<file2>`
**Relations:** Parent: <epic|story-key|none>; Blocks: <story keys|none>; Blocked by: <story keys|none>
**Label intent:** <semantic categories for this story>

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
- Include label intent per story to drive semantic label alignment.

## Approval gate (mandatory)

After presenting the stories, stop and ask for explicit approval. Adapt the question to the destination:

**GitHub:**

> Do you approve creating these stories as GitHub issues, applying parent/blocker relations, aligning labels, and adding them to the classic project column now?

**Notion:**

> Do you approve creating a new Kanban database in your Notion page, creating these stories as database pages, and applying parent/blocker relations now?

Do not create issues, pages, relations, labels, or project cards until explicit approval is received.

## Post-approval execution flow — GitHub

Uses GitHub CLI and GitHub GraphQL API.

### 1. Query existing repository labels

```bash
gh label list --repo OWNER/REPO --limit 200
```

### 2. Resolve labels per story

For each story, semantically map `label intent` to existing labels.

- If aligned existing labels are found, use them.
- If no aligned labels are found for a story, propose exactly **3** label suggestions.
- Ask user approval before creating any new label.
- Accept either one of the 3 suggested labels or a custom label name from the user.

### 3. Create approved missing labels

```bash
gh label create "<label_name>" --repo OWNER/REPO [--color "<hex_without_hash>"] [--description "<text>"]
```

### 4. Create one issue per story

```bash
gh issue create \
  --repo OWNER/REPO \
  --title "Story <epic>.<n> — <Story title>" \
  --body-file <story_markdown_file> \
  [--label "<resolved_label_1>" --label "<resolved_label_2>"] \
  [--assignee "<assignee>"] \
  [--milestone "<milestone_number>"]
```

### 5. Resolve issue node IDs

```bash
gh api repos/OWNER/REPO/issues/<issue_number> --jq '{id: .id, node_id: .node_id}'
```

### 6. Apply parent/sub-issue relations

```bash
gh api graphql -f query='
mutation($issueId: ID!, $subIssueId: ID!) {
  addSubIssue(input: {issueId: $issueId, subIssueId: $subIssueId}) {
    issue { id number }
    subIssue { id number }
  }
}' -F issueId=<PARENT_NODE_ID> -F subIssueId=<CHILD_NODE_ID>
```

### 7. Apply blocker relations

```bash
gh api graphql -f query='
mutation($issueId: ID!, $blockingIssueId: ID!) {
  addBlockedBy(input: {issueId: $issueId, blockingIssueId: $blockingIssueId}) {
    issue { id number }
    blockingIssue { id number }
  }
}' -F issueId=<BLOCKED_NODE_ID> -F blockingIssueId=<BLOCKER_NODE_ID>
```

### 8. Add cards to classic project column

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

### 9. Report (GitHub)

- Created issue list with URLs
- Final labels assigned per issue (existing vs. newly created)
- Applied parent/sub-issue and blocker relations
- Confirmation each card was added to the column
- Any failures and what input is needed to retry

## Post-approval execution flow — Notion

Uses Notion MCP tools (`notion-create-database`, `notion-create-view`, `notion-create-pages`, `notion-update-page`).

### 1. Create the Kanban database

Use `notion-create-database` with parent `notion_parent_page_id`. Title: `<epic_title> — Stories`.

Initial schema (self-relations added in step 2):

```sql
CREATE TABLE (
  "Name" TITLE,
  "Status" SELECT('To Do':gray, 'In Progress':blue, 'Done':green),
  "Category" SELECT('Frontend':orange, 'Backend':purple, 'Platform':pink),
  "Labels" MULTI_SELECT(),
  "Assignee" PEOPLE,
  "Milestone" RICH_TEXT,
  "Epic" RICH_TEXT
)
```

Capture the `data_source_id` from the response.

### 2. Add self-relation columns

Use `notion-update-data-source` with the resolved `data_source_id` to add:

- `"Blocks"` — `RELATION('<data_source_id>', DUAL 'Blocked By' 'blocked_by')`
- `"Parent Story"` — `RELATION('<data_source_id>', DUAL 'Sub Stories' 'sub_stories')`

The dual columns `Blocked By` and `Sub Stories` are created automatically.

### 3. Create a Kanban board view

Use `notion-create-view`:

- `type`: `board`
- `name`: `Kanban`
- `configure`: `GROUP BY "Status"`

### 4. Create one page per story

Use `notion-create-pages` with `parent.data_source_id`. For each story set:

- `Name` = `Story <epic>.<n> — <Story title>`
- `Status` = `To Do`
- `Category` = `<target_category>`
- `Labels` = resolved multi-select values matching `label intent`
- `Assignee` = user if provided
- `Milestone` = sprint name if provided
- `Epic` = `<epic_id> - <epic_title>`
- `content` = story Markdown body (Gherkin tasks)

Capture each created page's `id` and `url`.

### 5. Apply relations between pages

After all pages are created, use `notion-update-page` to patch each page's relation properties (`Blocks`, `Blocked By`, `Parent Story`, `Sub Stories`) using the page IDs resolved in step 4, according to `dependency_mode`, `parent_relation_mode`, and `relation_overrides`.

### 6. Report (Notion)

- Kanban board URL
- Created page URLs per story
- Applied parent/blocker relations
- Any failures and what input is needed to retry

## Reliability constraints

- Never fabricate IDs, URLs, or command results.
- Validate all relation targets exist before linking.
- Do not create labels (GitHub) or multi-select values (Notion) without explicit user approval.
- Avoid duplicate label creation when an equivalent existing label already matches.
- If any command or MCP tool call fails, show the exact error and request the missing/fixed input.
- If a matching story issue or page already exists, ask whether to skip, update, or create a new one.
