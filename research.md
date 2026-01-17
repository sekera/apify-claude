# Claude Code's project configuration architecture

Claude Code uses a `.claude/` folder to store project-level configurations that transform how the AI assistant understands and works with your codebase. The system supports **three distinct extension mechanisms**—subagents for specialized AI personas, skills for packaged expertise with progressive disclosure, and slash commands for quick reusable prompts—each with different invocation patterns and use cases.

## The `.claude/` folder structure explained

The complete directory structure follows a hierarchical organization that separates concerns between memory files, settings, and executable configurations:

```
your-project/
├── CLAUDE.md                      # Primary project memory (root)
├── CLAUDE.local.md                # Personal notes (gitignored)
├── .mcp.json                      # MCP server configuration
└── .claude/
    ├── CLAUDE.md                  # Alternative location for project memory
    ├── settings.json              # Hooks, permissions, environment (shared)
    ├── settings.local.json        # Personal overrides (gitignored)
    ├── agents/                    # Subagent definitions
    │   └── *.md                   # YAML frontmatter + system prompt
    ├── commands/                  # Slash commands
    │   └── *.md                   # Markdown with optional frontmatter
    ├── skills/                    # Agent Skills directories
    │   └── skill-name/
    │       └── SKILL.md           # Required skill definition
    └── rules/                     # Modular rule files (auto-loaded)
        └── *.md                   # Scoped instructions
```

User-level configuration mirrors this structure at `~/.claude/`, providing personal defaults that apply across all projects. **Project-level configurations take precedence** over user-level when names conflict.

## CLAUDE.md is the memory backbone

The CLAUDE.md file serves as Claude Code's persistent memory, automatically loaded into context when starting any conversation. According to Anthropic's engineering blog, this makes it ideal for documenting common bash commands, core files, code style guidelines, testing instructions, and repository etiquette.

**Format requirements are minimal**—plain Markdown with no required schema. Anthropic recommends keeping content concise and human-readable. A practical example:

```markdown
# Bash commands
- npm run build: Build the project
- npm run typecheck: Run the typechecker

# Code style
- Use ES modules (import/export) syntax, not CommonJS
- Destructure imports when possible

# Workflow
- Typecheck when done making code changes
- Prefer running single tests for performance
```

The **memory hierarchy** flows from enterprise policy (organization-wide) → project memory (team-shared) → user memory (personal defaults) → project local memory (personal project-specific). Files support an `@path/to/import` syntax for referencing external content, with recursive imports up to **5 hops depth**.

Research from HumanLayer indicates optimal CLAUDE.md files stay **under 60-150 lines** for root files—every line competes for attention in the context window, and LLMs reliably follow approximately 150-200 instructions.

## Subagents provide specialized AI personas

Subagents are pre-configured AI personas operating with **separate context windows**, preventing main conversation pollution while enabling specialized expertise. They're stored as Markdown files with YAML frontmatter in `.claude/agents/`.

**File format example:**

```yaml
---
name: code-reviewer
description: Reviews code for quality and best practices. Use proactively after code changes.
tools: Read, Glob, Grep, Bash
model: sonnet
permissionMode: default
---

You are a senior code reviewer ensuring high standards.

When invoked:
1. Run git diff to see recent changes
2. Focus on modified files
3. Begin review immediately
```

The `name` field uses lowercase letters and hyphens. The `description` field is **critical for auto-delegation**—Claude uses this to determine when to invoke the subagent. The `tools` field restricts available capabilities (omit to inherit all). Model options include `sonnet`, `opus`, `haiku`, or `inherit`.

Claude Code includes three **built-in subagents**: the general-purpose agent for complex multi-step tasks, the Plan agent for research in plan mode, and the Explore agent (Haiku model) optimized for fast read-only codebase exploration.

## Skills package expertise with progressive disclosure

Skills are **model-invoked capabilities**—Claude autonomously decides when to use them based on your request and the skill's description. Unlike slash commands, you don't explicitly trigger them.

Each skill lives in its own directory with a required `SKILL.md` file:

```
pdf-processing/
├── SKILL.md              # Required - main instructions
├── FORMS.md              # Optional documentation
├── REFERENCE.md          # Optional reference
└── scripts/
    └── fill_form.py      # Optional utilities
```

**SKILL.md structure:**

```yaml
---
name: pdf-processing
description: Extract text, fill forms, merge PDFs. Use when working with PDF files.
allowed-tools: Read, Grep, Write, Bash
---

# PDF Processing

## Quick start
Extract text with pdfplumber...

For form filling, see [FORMS.md](FORMS.md).
```

The `name` field must use **lowercase letters, numbers, and hyphens only** (max 64 characters). The `description` has a 1024-character maximum and is crucial—Claude uses semantic matching against it to determine skill relevance.

**Progressive disclosure** works in stages: Level 1 loads only name and description at startup. Level 2 loads full SKILL.md when triggered. Level 3+ loads additional referenced files only when Claude needs them. This architecture keeps context windows efficient until information is actually required.

## Slash commands offer quick explicit prompts

Commands are **user-invoked** prompts you trigger explicitly by typing `/command-name`. They're stored as single Markdown files in `.claude/commands/`, where the filename becomes the command name.

**Format with frontmatter:**

```yaml
---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git commit:*)
argument-hint: [issue-number] [priority]
description: Fix a GitHub issue
model: claude-3-5-haiku-20241022
---

Fix issue #$1 with priority $2.

Current branch: !`git branch --show-current`
Recent commits: !`git log --oneline -5`
```

Commands support **positional arguments** (`$1`, `$2`) or `$ARGUMENTS` for all arguments as a single string. The `!` prefix executes shell commands inline. The `@` symbol references file contents.

**Namespacing** through subdirectories creates organized command hierarchies—`.claude/commands/git/commit.md` becomes `/project:git:commit`.

## When to use agents versus skills versus commands

| Scenario | Best choice | Rationale |
|----------|-------------|-----------|
| Code review after commits | **Subagent** | Separate context, specialized persona |
| Complex multi-file workflow | **Skill** | Auto-discovery, scripts, progressive disclosure |
| Quick repetitive prompt | **Command** | Explicit trigger, single file simplicity |
| Task needing isolation | **Subagent** | Own context window, parallel processing |
| Team-standardized guidance | **Skill** | Multiple reference files, git-shareable |
| Security/debugging checks | **Command** | Explicit control over execution |

The fundamental distinction: **subagents get separate context windows and can run parallel tasks**, **skills load progressively and are auto-discovered**, **commands require explicit invocation**.

## Converting monolithic docs to modular structure

Community patterns for modularization emphasize several approaches. The **@imports system** keeps root CLAUDE.md lean by referencing external files: `See @docs/api-patterns.md for API conventions`. The **.claude/rules/ directory** automatically loads all Markdown files at the same priority as CLAUDE.md—simply drop files and they're included without explicit imports.

**Path-scoped rules** use YAML frontmatter to restrict instructions to specific file patterns:

```yaml
---
paths:
  - "src/api/**/*.ts"
---

# API Development Rules
- All endpoints must include input validation
```

**Subdirectory CLAUDE.md files** provide location-specific context, loaded only when Claude works in that subtree. The project [ChrisWiles/claude-code-showcase](https://github.com/ChrisWiles/claude-code-showcase) with **3,800+ stars** demonstrates comprehensive implementation including hooks, skills, agents, commands, and GitHub Actions automation.

## Conclusion

Claude Code's configuration architecture separates concerns through three complementary mechanisms with distinct invocation patterns and context management approaches. Subagents excel at isolated, specialized tasks requiring their own context. Skills provide auto-discovered, progressively-loaded expertise for complex workflows. Commands offer quick, explicit access to reusable prompts. The most effective configurations keep root CLAUDE.md files lean (under 150 lines), leverage the rules directory for modular team-owned instructions, and use skills for detailed procedural knowledge that loads only when needed. Community evidence from highly-starred repositories demonstrates that thoughtful architecture—not extensive documentation—produces the best Claude Code experiences.