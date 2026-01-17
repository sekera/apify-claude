# Claude Code setup for Apify actors development

Claude Code configuration for Apify actor development.

Transforms the standard `AGENTS.md` approach into a structured `.claude/` folder setup with separated concerns.

## Claude setup structure

This setup is based on the [Crawlee + Playwright + Chrome](https://github.com/apify/actor-templates/tree/master/templates/ts-crawlee-playwright-chrome) template, where the original [`AGENTS.md`](https://github.com/apify/actor-templates/blob/master/templates/ts-crawlee-playwright-chrome/AGENTS.md) file is transformed into a specialized Claude Code setup.

The structure below excludes the actual actor code (the open-sourced "Playwright Scraper"):

```
.claude/
├── agents/
│   ├── apify-ts-coder.md      # TypeScript/Crawlee development
│   ├── code-reviewer.md       # Code review
│   ├── selector-analyzer.md   # CSS/XPath selector analysis
│   └── test-runner.md         # Test execution
├── commands/
│   ├── commit.md              # Git commit workflow
│   ├── deploy.md              # Deploy to Apify platform
│   ├── deploy-and-get-working.md  # Deploy with error fixing loop
│   ├── lint.md                # Linting
│   ├── review-claude-setup.md # Review and fix Claude Code config
│   ├── run.md                 # Local run
│   └── validate.md            # Validation
├── rules/
│   └── apify-patterns.md      # Apify-specific patterns
├── skills/
│   ├── actor-scaffold/        # Actor scaffolding templates
│   ├── apify-ops/             # Platform operations (MCP, CLI, API)
│   ├── apify-schemas/         # Input/output/dataset schemas
│   └── skill-creator/         # Skill creation utilities
└── settings.json              # Permissions configuration

.mcp.json                      # Apify MCP server configuration
AGENTS.md                      # Original Apify development guide
CLAUDE.md                      # Project-level Claude Code config
```

## How do I get it working?

1. Create a `.env` file with `APIFY_TOKEN` filled in. Use [`.env.template`](.env.template) as a template.
2. Create an actor in the [Apify console](https://console.apify.com/) and configure it in [`.actor/actor.json`](.actor/actor.json).
3. Start Claude Code and run the `/deploy-and-get-working` slash command. It will push the code to the Apify platform and compile it.

## Related

- [Claude Code documentation](https://code.claude.com/docs/en/)
- [Apify documentation](https://docs.apify.com/)
- [Crawlee documentation](https://crawlee.dev/)
