# Improvement Categories

Types of improvements the reflect skill can suggest.

---

## 1. Skill Content Updates

### Pattern Updates

**When:** API syntax, function signatures, or conventions have changed

**What to update:**
- Code examples in SKILL.md
- Reference files with patterns
- Type definitions and schemas

**Example:**
```diff
## Before
- const result = db.query("users").collect();
- "priority": 1

## After
+ const result = await ctx.db.query("users").collect();
+ "priority": "high"
```

### Missing Context

**When:** Claude had to discover information through exploration

**What to add:**
- New reference files for undocumented topics
- Additional examples for edge cases
- Navigation hints for common lookups

---

## 2. Frontmatter Improvements

### Description Triggers

**When:** Skill wasn't activated when it should have been

**Fix:** Add trigger terms to description

```yaml
# Before
description: Handles backend operations

# After
description: Handles backend operations including Convex queries, mutations, and actions. Use when working with database, API endpoints, or server functions.
```

### Tool Restrictions

**When:** Skill uses tools it shouldn't, or lacks needed tools

**Fix:** Add/modify allowed-tools

```yaml
# Add Bash for API calls
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash  # Added for curl commands
```

---

## 3. Reference File Additions

### New Reference File

**When:** Skill needs more detailed documentation for a subtopic

**Structure:**
```
skill/
├── SKILL.md
└── reference/
    ├── existing.md
    └── new-topic.md  # Add this
```

**Common new reference files:**
- `api-schema.md` - Document API field types and validation
- `patterns.md` - Common code patterns and examples
- `troubleshooting.md` - Known issues and solutions

### Reference File Updates

**When:** Existing reference is incomplete or outdated

**Signs:**
- Information had to be discovered that should have been documented
- Examples don't match current implementation
- Missing edge cases that caused errors

---

## 4. Schema Documentation

### API Schema Updates

**When:** API calls fail due to schema mismatches

**What to document:**
```markdown
## Task API Schema

### Required Fields
| Field | Type | Valid Values |
|-------|------|--------------|
| projectId | string | "proj_xxxxx" |
| title | string | Any text |
| type | string | "feature", "bugfix", "refactor", "chore", "docs" |

### Optional Fields
| Field | Type | Valid Values | Default |
|-------|------|--------------|---------|
| priority | string | "low", "medium", "high", "critical" | "medium" |
| status | string | "pending", "in_progress", "completed", "blocked" | "pending" |
| domain | string | "frontend", "backend", "database", "infrastructure", "fullstack" | - |
```

---

## 5. Hook Additions

### Validation Hooks

**When:** Repeated environment/prerequisite failures

**Add to settings.json or settings.local.json:**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/scripts/validate-env.sh"
          }
        ]
      }
    ]
  }
}
```

---

## 6. Agent Improvements

### Skill Injection

**When:** Agent repeatedly needs context from a skill

**Fix:** Add skills field to agent definition

```yaml
# In agent file
skills:
  - backend
  - api
```

### Model Optimization

**When:** Agent using wrong model for task complexity

**Fix:** Adjust model field based on task requirements

---

## 7. Command Updates

### Parameter Documentation

**When:** Command parameters are unclear or incorrect

**What to update:**
- Parameter types and validation
- Example invocations
- Error handling documentation

### Workflow Steps

**When:** Command workflow has gaps or incorrect ordering

**What to update:**
- Step-by-step instructions
- Decision trees
- Error recovery paths

---

## 8. Structural Changes

### Context Fork

**When:** Skill is heavy and polluting main context

**Fix:** Add `context: fork` to frontmatter and pair with dedicated agent

### File Reorganization

**When:** SKILL.md is too long (>500 lines)

**Fix:** Split into:
- Main SKILL.md (<500 lines) - Overview and flow
- reference/ subdirectory - Detailed documentation by topic

---

## Improvement Templates

### Template: Add Missing Schema

```markdown
## [API Name] Schema

### Endpoint
`[METHOD] [path]`

### Request Body
| Field | Type | Required | Valid Values |
|-------|------|----------|--------------|
| field1 | type | Yes/No | values |

### Response
| Field | Type | Description |
|-------|------|-------------|
| field1 | type | description |

### Example
\`\`\`bash
curl -X [METHOD] "[url]" \\
  -H "Content-Type: application/json" \\
  -d '{...}'
\`\`\`
```

### Template: Add Pattern Documentation

```markdown
## [Pattern Name]

### When to Use
[Description of when this pattern applies]

### Correct Usage
\`\`\`[language]
// Correct example
\`\`\`

### Common Mistakes
\`\`\`[language]
// Wrong - [why it's wrong]
\`\`\`

### Related Patterns
- [Link to related pattern]
```
