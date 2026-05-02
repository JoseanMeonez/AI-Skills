# AI Skills Marketplace

Hi everyone 👋

This started as my AI planning playground, and now it’s a tiny plugin marketplace.

I noticed that when feature tasks are written with Gherkin scenarios, AI planning gets way more useful (and way less “vibe TODOs”). So this repo packages that workflow into installable plugins.

## Available Plugins

Currently, this marketplace offers the following plugins:

- **feature-story-planner**: Plan features with Gherkin-driven technical stories and automate GitHub/Notion delivery.
- **playwright**: Browser automation and end-to-end testing tools.
- **csharp-lsp**: C# language server for code intelligence.
- **frontend-design**: Specialized skill for UI/UX implementation.
- **code-simplifier**: Automated refactoring to improve code clarity and maintainability.
- **typescript-lsp**: Enhanced intelligence for TypeScript and JavaScript.
- **skill-creator**: Meta-skill to build, optimize, and benchmark other skills.
- **code-review**: Automated multi-agent code review system.
- **superpowers**: Core library for TDD, systematic debugging, and professional workflows.
- **ui-ux-pro-max**: Advanced design intelligence with support for dozens of styles and frameworks.

## Marketplace shape

- Registry: `.claude-plugin/marketplace.json`
- Plugins: `plugins/<plugin-name>/`
- Install via Claude Code: `/plugin install <plugin-name>@ai-skills`

That first plugin helps you turn a feature into structured technical stories, then create related GitHub issues, labels, and board cards through `gh`.

In short: plan first, code second, panic later.  
(Hopefully much later.)
