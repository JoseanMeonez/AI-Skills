# AI Skills Marketplace

Hi everyone 👋

This started as my AI planning playground, and now it’s a tiny plugin marketplace.

I noticed that when feature tasks are written with Gherkin scenarios, AI planning gets way more useful (and way less “vibe TODOs”). So this repo packages that workflow into installable plugins.

## Marketplace shape

- Registry: `.claude-plugin/marketplace.json`
- Plugins: `plugins/<plugin-name>/`
- First plugin: `plugins/feature-story-planner/`
- Install via Claude Code: `/plugin install feature-story-planner@ai-skills`

That first plugin helps you turn a feature into structured technical stories, then create related GitHub issues, labels, and board cards through `gh`.

In short: plan first, code second, panic later.  
(Hopefully much later.)
