# apify-claude

Claude Code configuration for Apify actor development.

Transforms the standard `AGENTS.md` approach into a structured `.claude/` folder setup with separated concerns.

## Structure

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

## Related

- [Claude Code documentation](https://code.claude.com/docs/en/)
- [Apify documentation](https://docs.apify.com/)
- [Crawlee documentation](https://crawlee.dev/)
