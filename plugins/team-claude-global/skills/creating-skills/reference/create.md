# Create Mode

Guide for authoring effective Claude Code skills following Anthropic's best practices.

## Contents

- [Core Principles](#core-principles)
- [SKILL.md Structure](#skillmd-structure)
- [Directory Layout](#directory-layout)
- [Progressive Disclosure](#progressive-disclosure)
- [Description Guidelines](#description-guidelines)

---

## Core Principles

### Conciseness is Key

The context window is shared. Only add context Claude doesn't already have.

**Challenge each piece:**
- "Does Claude really need this explanation?"
- "Can I assume Claude knows this?"
- "Does this paragraph justify its token cost?"

### Keep SKILL.md Under 500 Lines

If approaching this limit, split content into reference files and link clearly.

### Set Appropriate Degrees of Freedom

| Freedom Level | When to Use | Example |
|---------------|-------------|---------|
| **High** (text instructions) | Multiple approaches valid | Code review guidelines |
| **Medium** (pseudocode) | Preferred pattern exists | Report templates |
| **Low** (specific scripts) | Operations are fragile | Database migrations |

---

## SKILL.md Structure

Every SKILL.md must start with YAML frontmatter:

```yaml
---
name: my-skill-name
description: What it does and when to use it. Use when [specific triggers].
---
```

### Frontmatter Requirements

**`name`** (required):
- Maximum 64 characters
- Lowercase letters, numbers, hyphens only
- Use gerund form (verb + -ing): `processing-pdfs`, `managing-databases`
- No reserved words: "anthropic", "claude"

**`description`** (required):
- Maximum 1024 characters
- Write in **third person** (not "I can help you" or "You can use this")
- Include both what the skill does AND when to use it
- Be specific with trigger terms

### Body Structure

```markdown
# Skill Title

## Contents

- Table of contents for quick scanning

## Quick Start

- Essential info to get started immediately

## Reference Files

- Links to detailed documentation (if using progressive disclosure)

## [Topic Sections]

- Core content organized by topic
```

---

## Directory Layout

### Simple Skill (single file)

```
my-skill/
└── SKILL.md
```

### Skill with References (recommended for complex skills)

```
my-skill/
├── SKILL.md              # Overview + navigation (<500 lines)
└── reference/
    ├── topic-a.md        # Detailed content
    ├── topic-b.md        # Detailed content
    └── examples.md       # Code examples
```

### Skill with Scripts

```
my-skill/
├── SKILL.md
├── reference/
│   └── api.md
└── scripts/
    ├── validate.py       # Utility scripts (executed, not loaded)
    └── process.sh
```

---

## Progressive Disclosure

SKILL.md serves as an overview that points to detailed materials as needed.

**Pattern: High-level guide with references**

```markdown
# PDF Processing

## Quick start

Extract text with pdfplumber:
\`\`\`python
import pdfplumber
with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
\`\`\`

## Advanced features

**Form filling**: See [reference/forms.md](reference/forms.md)
**API reference**: See [reference/api.md](reference/api.md)
```

### Key Rules

1. **Keep references one level deep** - Link directly from SKILL.md to reference files
2. **No deep nesting** - Avoid file A → file B → file C chains
3. **Add table of contents** for files over 100 lines
4. **Name files descriptively** - `form_validation.md` not `doc2.md`

---

## Description Guidelines

The description enables skill discovery. Claude uses it to select the right skill.

### Good Examples

```yaml
description: Extracts text and tables from PDF files, fills forms, merges documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
```

```yaml
description: Analyzes Excel spreadsheets, creates pivot tables, generates charts. Use when analyzing Excel files, spreadsheets, tabular data, or .xlsx files.
```

### Avoid

```yaml
description: Helps with documents  # Too vague
description: I can help you process PDFs  # Wrong person (use third person)
description: Processes data  # Not specific enough
```

---

## Creation Workflow

### Step 1: Define Purpose

Answer these questions:
- What specific problem does this skill solve?
- When should Claude use this skill?
- What trigger terms should activate it?

### Step 2: Choose Structure

- **Simple skill**: Single SKILL.md under 500 lines
- **Complex skill**: SKILL.md + reference files

### Step 3: Write Frontmatter

```yaml
---
name: {lowercase-hyphenated-name}
description: {what it does}. Use when {trigger conditions}.
---
```

### Step 4: Add Content

Start with Quick Start section, then add topic sections.

### Step 5: Validate

Run the quality checklist from [reference/checklist.md](checklist.md).

---

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Over 500 lines in SKILL.md | Split into reference files |
| Vague description | Add specific trigger terms |
| Wrong person in description | Use third person |
| Deep file nesting | Keep references one level deep |
| Missing table of contents | Add for files over 100 lines |
