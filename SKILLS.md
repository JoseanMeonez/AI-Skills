# Skills Inventory

Last reviewed: 2026-10-24

This repository vendors only skills authored here. The external packs it uses are referenced by source, never copied.

## Vendored

| Skill | Plugin | Author |
| --- | --- | --- |
| feature-story-planner | `plugins/feature-story-planner` | AI Skills Maintainers (this repo) |

## Referenced packs

| Pack | Author / license | Skills in use | Source | Installed at |
| --- | --- | --- | --- | --- |
| superpowers | Jesse Vincent / MIT | brainstorming, dispatching-parallel-agents, executing-plans, finishing-a-development-branch, receiving-code-review, requesting-code-review, subagent-driven-development, systematic-debugging, test-driven-development, using-git-worktrees, using-superpowers, verification-before-completion, writing-plans, writing-skills | https://github.com/obra/superpowers | `~/.gemini/antigravity-cli/skills` |
| Anthropic official | Anthropic | code-review, code-simplifier, frontend-design, skill-creator, csharp-lsp, typescript-lsp, playwright | https://github.com/anthropics/claude-plugins-official | `~/.claude/plugins/marketplaces/claude-plugins-official` |
| ui-ux-pro-max | nextlevelbuilder / MIT | ui-ux-pro-max, banner-design, brand, design, design-system, slides, ui-styling | Claude plugin marketplace `ui-ux-pro-max-skill` | `~/.claude/plugins/marketplaces/ui-ux-pro-max-skill` |
| ponytail | `@dietrichgebert` | ponytail, ponytail-review, ponytail-audit, ponytail-debt, ponytail-gain, ponytail-help | npm `@dietrichgebert/ponytail` | `~/.config/opencode/node_modules` |

## Adding a pack to this repo

Only do this for skills you author or deliberately want to fork:

1. Copy the plugin into `plugins/<plugin-name>/`.
2. Add it to `.claude-plugin/marketplace.json`.
3. Add a row to the vendored table above.

Anything else stays referenced, not copied.
