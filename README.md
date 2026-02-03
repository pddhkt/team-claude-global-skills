# Team Claude Global Skills

Claude Code plugin providing universal development skills available across all projects.

## Installation

### Add the Marketplace

```bash
/plugin marketplace add pddhkt/team-claude-global-skills
```

### Install the Plugin

```bash
# User scope - available in all your projects (recommended)
/plugin install team-claude-global@team-claude-global-skills

# Project scope - shared with collaborators via .claude/settings.json
/plugin install team-claude-global@team-claude-global-skills --scope project
```

## Available Skills

| Skill | Usage | Description |
|-------|-------|-------------|
| git | `/team-claude-global:git` | Git commit conventions and best practices |
| smart-commit | `/team-claude-global:smart-commit` | Batch commit grouping by logical change |
| test | `/team-claude-global:test` | Test generation patterns |
| agent-browser | `/team-claude-global:agent-browser` | Headless browser automation |
| creating-skills | `/team-claude-global:creating-skills` | Meta-skill for authoring new skills |
| install-v2 | `/team-claude-global:install-v2` | Plugin installation workflow |
| reflect | `/team-claude-global:reflect` | Session reflection and skill improvement |

## Skills Overview

### git

Git commit conventions and best practices:
- Conventional commit message formatting
- Branch naming conventions
- Commit scope and type guidelines

### smart-commit

Intelligent batch commit grouping:
- Groups related changes into logical commits
- Analyzes file changes to determine commit boundaries
- Generates meaningful commit messages

### test

Test generation patterns:
- Unit test scaffolding
- Integration test patterns
- Test-driven development workflows

### agent-browser

Headless browser automation:
- Web scraping techniques
- Browser testing patterns
- Troubleshooting guide

**Reference files:**
- `scraping.md` - Web scraping techniques
- `testing.md` - Browser testing patterns
- `troubleshooting.md` - Debugging guide

### creating-skills

Meta-skill for authoring new Claude Code skills:
- Skill creation workflow
- Template patterns
- Review guidelines
- Frontmatter reference

**Reference files:**
- `create.md` - Creation workflow
- `templates.md` - Skill templates
- `patterns.md` - Pattern conventions
- `checklist.md` - Creation checklist
- `review.md` - Review guidelines
- `analysis-prompts.md` - Analysis prompts
- `frontmatter-reference.md` - Frontmatter format

### install-v2

Plugin installation workflow for setting up Team Claude in projects.

### reflect

Session reflection and skill improvement:
- Analyze current conversation for improvements
- Categorize improvement types
- Sync strategy for pushing changes back

**Reference files:**
- `session-analysis.md` - Session metrics and analysis
- `improvement-categories.md` - Improvement categorization
- `sync-strategy.md` - Sync strategies

## Updating

Get the latest skill improvements:

```bash
/plugin marketplace update team-claude-global-skills
```

## Contributing

When you discover better patterns while working:

1. Run `/team-claude-global:reflect`
2. Review the proposed changes
3. Approve to push improvements to this repository
4. Other team members get updates with `/plugin marketplace update`

## Repository Structure

```
team-claude-global-skills/
├── .claude-plugin/
│   └── marketplace.json
│
└── plugins/
    └── team-claude-global/
        ├── .claude-plugin/
        │   └── plugin.json
        └── skills/
            ├── agent-browser/
            ├── git/
            ├── smart-commit/
            ├── test/
            ├── creating-skills/
            ├── install-v2/
            └── reflect/
```

## License

MIT
