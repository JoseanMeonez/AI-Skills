# Skills Inventory

Last reviewed: 2026-10-24

This repository vendors **only skills authored here**. Every other skill in use is referenced by its upstream source instead of copied, so upgrades happen upstream and licenses stay with their owners.

## Vendored in this repo

| Skill | Plugin | Author |
| --- | --- | --- |
| feature-story-planner | `plugins/feature-story-planner` | AI Skills Maintainers (this repo) |

## Referenced external packs

| Pack | Author / license | Skills in use | Source |
| --- | --- | --- | --- |
| superpowers 5.0.7 | Jesse Vincent / MIT | brainstorming, dispatching-parallel-agents, executing-plans, finishing-a-development-branch, receiving-code-review, requesting-code-review, subagent-driven-development, systematic-debugging, test-driven-development, using-git-worktrees, using-superpowers, verification-before-completion, writing-plans, writing-skills | https://github.com/obra/superpowers |
| gstack | upstream unverified | autoplan, benchmark, benchmark-models, browse, canary, careful, codex, context-restore, context-save, cso, design-consultation, design-html, design-review, design-shotgun, devex-review, document-generate, document-release, freeze, guard, health, investigate, ios-clean, ios-design-review, ios-fix, ios-qa, ios-sync, land-and-deploy, landing-report, learn, make-pdf, office-hours, open-gstack-browser, pair-agent, plan-ceo-review, plan-design-review, plan-devex-review, plan-eng-review, plan-tune, qa, qa-only, retro, review, scrape, setup-browser-cookies, setup-deploy, setup-gbrain, ship, skillify, spec, sync-gbrain, unfreeze | `~/.claude/skills/gstack` |
| Gentle AI workflow pack | package-owned | sdd-init, sdd-explore, sdd-propose, sdd-spec, sdd-design, sdd-tasks, sdd-apply, sdd-verify, sdd-archive, sdd-onboard, skill-registry, skill-improver, skill-creator, work-unit-commits, chained-pr, branch-pr, comment-writer, issue-creation, judgment-day, go-testing, graphify, cognitive-doc-design | `~/.config/opencode/skills` |
| Anthropic plugin set | Anthropic | code-review, code-simplifier, frontend-design, skill-creator, csharp-lsp, typescript-lsp | `~/.claude/plugins/marketplaces` |
| playwright | Microsoft | playwright MCP server | Claude plugin install |
| ui-ux-pro-max 2.5.0 | nextlevelbuilder / MIT | ui-ux-pro-max | Claude plugin install |
| ckm design pack | upstream unverified | banner-design, brand, design, design-system, slides, ui-styling, ui-ux-pro-max | Claude plugin marketplace `ui-ux-pro-max-skill` |
| Anthropic synced bundle | Anthropic | docs, docx, xlsx, pptx, pdf, google-workspace, chrome-browser, built-in-browser, computer-use, deep-research, morning, financial-auditor, import-memory, skill-creator | `~/.claude/skills/synced` |
| ponytail 4.8.4 | `@dietrichgebert` | ponytail (lite/full/ultra) | npm dependency of `~/.config/opencode` |

## Candidate skills to vendor later

These are installed outside this repo but look authored or customized here. Move them under `plugins/` if they should be owned by this repository.

- `financial-auditor`: personal finance workflow tied to a specific Gmail/Notion setup.
- `morning`: personal brief workflow, if it has local customizations.

## Adding a pack to this repo

Only do this for skills you author or deliberately want to fork:

1. Copy the plugin into `plugins/<plugin-name>/`.
2. Add it to `.claude-plugin/marketplace.json`.
3. Add a row to the vendored table above.

Anything else stays referenced, not copied.
