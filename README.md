# AI Skills Marketplace

Hi everyone 👋

This started as my AI planning playground, and now it’s a tiny plugin marketplace.

I noticed that when feature tasks are written with Gherkin scenarios, AI planning gets way more useful (and way less “vibe TODOs”). So this repo packages that workflow into installable plugins.

## Available Plugins

This marketplace only ships skills authored and maintained here:

- **feature-story-planner**: Plan features with Gherkin-driven technical stories and automate GitHub/Notion delivery.

Third-party packs I use (superpowers, Anthropic official, ui-ux-pro-max, Orca, ponytail) are **referenced by source, not copied**. See [SKILLS.md](SKILLS.md) for the skills and install locations.

## Marketplace shape

- Registry: `.claude-plugin/marketplace.json`
- Plugins: `plugins/<plugin-name>/`
- Install via Claude Code: `/plugin install <plugin-name>@ai-skills`

That first plugin helps you turn a feature into structured technical stories, then create related GitHub issues, labels, and board cards through `gh`.

In short: plan first, code second, panic later.  
(Hopefully much later.)
