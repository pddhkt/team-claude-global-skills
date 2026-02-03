---
name: skills
description: Creates and reviews Claude Code skills, commands, and agents. Use when authoring new skills, improving existing configurations, or learning skill best practices.
allowed-tools:
  - Read
  - Grep
  - Glob
  - Edit
  - Write
  - AskUserQuestion
---

# Skills Skill

Create new skills or improve existing ones. Guides you through authoring best practices and configuration patterns.

## Contents

- [Quick Start](#quick-start)
- [Interactive Flow](#interactive-flow)
- [Mode References](#mode-references)
- [Common Concepts](#common-concepts)
- [Reference Files](#reference-files)

---

## Quick Start

```bash
/skills                             # Starts interactive session
/skills "my-new-feature"            # Optional: provide skill name upfront
```

The skill guides you through questions - no flags needed.

---

## Interactive Flow

### Step 1: Mode Selection

```
AskUserQuestion:
  question: "What would you like to do with skills?"
  header: "Mode"
  options:
    - label: "Create"
      description: "Author a new skill from scratch"
    - label: "Review"
      description: "Analyze and improve an existing skill"
    - label: "Learn"
      description: "Explore skill authoring best practices"
```

### Step 2a: Create Mode - Skill Name

```
AskUserQuestion:
  question: "What should the skill be named?"
  header: "Name"
  options:
    - label: "Enter name"
      description: "Provide a name (lowercase, hyphens, gerund form)"
```

### Step 2b: Review Mode - Target Selection

```
AskUserQuestion:
  question: "Which component do you want to review?"
  header: "Target"
  options:
    - label: "Browse skills"
      description: "List available skills to choose from"
    - label: "Browse commands"
      description: "List available commands"
    - label: "Browse agents"
      description: "List available agents"
    - label: "Enter path"
      description: "Provide specific file path"
```

### Step 2c: Learn Mode - Topic Selection

```
AskUserQuestion:
  question: "What would you like to learn about?"
  header: "Topic"
  options:
    - label: "Skill structure"
      description: "SKILL.md format, frontmatter, directory layout"
    - label: "Templates"
      description: "Starting templates for different skill types"
    - label: "Patterns"
      description: "Improvement patterns and best practices"
    - label: "Frontmatter reference"
      description: "Complete field documentation"
```

---

## Mode References

| Mode | Reference | Description |
|------|-----------|-------------|
| **Create** | [reference/create.md](reference/create.md) | Core principles, structure, directory layout |
| **Review** | [reference/review.md](reference/review.md) | 8-step refinement flow, pattern analysis |
| **Learn** | Various reference files | Templates, checklist, patterns, frontmatter |

Read the appropriate reference file based on user's mode selection.

---

## Common Concepts

### Component Types

| Type | Location | Purpose |
|------|----------|---------|
| **Skill** | `.claude/skills/{name}/SKILL.md` | Reusable domain knowledge |
| **Command** | `.claude/commands/{name}.md` | User-invocable actions |
| **Agent** | `.claude/agents/{name}.md` | Specialized executors |

### Frontmatter Essentials

Every component needs:

```yaml
---
name: my-skill-name       # lowercase, hyphens, max 64 chars
description: What it does AND when to use it. Use when [triggers].
---
```

### Directory Layouts

**Simple skill:**
```
my-skill/
└── SKILL.md
```

**Skill with references:**
```
my-skill/
├── SKILL.md              # Overview (<500 lines)
└── reference/
    ├── topic-a.md
    └── topic-b.md
```

---

## Reference Files

Detailed documentation split by topic:

| Topic | File | Description |
|-------|------|-------------|
| **Creating** | [reference/create.md](reference/create.md) | Core principles, structure guidelines |
| **Reviewing** | [reference/review.md](reference/review.md) | 8-step refinement workflow |
| **Templates** | [reference/templates.md](reference/templates.md) | Starting templates for skills |
| **Checklist** | [reference/checklist.md](reference/checklist.md) | Quality verification before sharing |
| **Patterns** | [reference/patterns.md](reference/patterns.md) | Improvement pattern catalog |
| **Prompts** | [reference/analysis-prompts.md](reference/analysis-prompts.md) | Questions for gathering preferences |
| **Frontmatter** | [reference/frontmatter-reference.md](reference/frontmatter-reference.md) | Complete field documentation |

---

## Quick Validation

```bash
# Check line count (should be <500)
wc -l SKILL.md

# Verify reference links exist
grep -oE '\[.*\]\(.*\.md\)' SKILL.md | while read link; do
  file=$(echo "$link" | grep -oE '\(.*\)' | tr -d '()')
  [ -f "$file" ] || echo "Missing: $file"
done

# Check for Windows paths
grep -E '\\\\' SKILL.md && echo "Found Windows-style paths"
```

---

## Related Skills

| Skill | Relationship |
|-------|--------------|
| `/init-project` | Sets up project context |
| `/planning` | Plans feature implementation |
| `/exploration` | Explores codebase patterns |
