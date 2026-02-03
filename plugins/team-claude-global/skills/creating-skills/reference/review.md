# Review Mode

Interactive refinement of Claude Code components. Analyzes configurations, proposes improvement patterns, gathers preferences, and applies changes.

## Contents

- [How to Use](#how-to-use)
- [Refinement Flow](#refinement-flow)
- [Step-by-Step Instructions](#step-by-step-instructions)
- [Quick Pattern Reference](#quick-pattern-reference)

---

## How to Use

This mode works with **one component per session** through an iterative refinement cycle.

**Start a review:**
```
"Review .claude/skills/common/init-project/SKILL.md"
"Review the api skill"
"Help me improve the scout agent"
```

**Or list available components:**
```
"What skills can I review?"
"Show me my agents"
```

---

## Refinement Flow

```
┌─────────────────────────────────────────────────────────────┐
│ Step 1: Target Selection                                    │
│   User provides skill/command/agent path                    │
│   OR list available components for selection                │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│ Step 2: Read & Analyze                                      │
│   - Read target file                                        │
│   - Identify component type                                 │
│   - Parse current frontmatter                               │
│   - Analyze body structure                                  │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│ Step 3: Present Current State                               │
│   - Show current frontmatter                                │
│   - Highlight what's configured vs missing                  │
│   - Show component-specific checklist results               │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│ Step 4: Propose Applicable Patterns                         │
│   Based on component type and analysis, suggest patterns    │
│   User selects which patterns to apply                      │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│ Step 5: Gather Preferences                                  │
│   For selected patterns, ask targeted questions             │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│ Step 6: Generate Recommendations                            │
│   - Show before/after frontmatter diff                      │
│   - Explain each change                                     │
│   - Provide severity: critical / suggested / optional       │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│ Step 7: Apply Changes                                       │
│   Ask: "Apply these changes?"                               │
│   - Apply selected improvements                             │
│   - Show final result                                       │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│ Step 8: Continue or Done                                    │
│   "Continue refining or done with this component?"          │
│   → Loop back to Step 3 if continuing                       │
└─────────────────────────────────────────────────────────────┘
```

---

## Step-by-Step Instructions

### Step 1: Target Selection

If the user provides a path, use it directly. Otherwise, help them select:

```bash
# List commands
ls .claude/commands/*.md 2>/dev/null

# List skills
ls .claude/skills/*/SKILL.md 2>/dev/null

# List agents
ls .claude/agents/*.md 2>/dev/null
```

### Step 2: Read & Analyze

Read the target file and identify:

1. **Component type** based on path:
   - `.claude/commands/` → Command
   - `.claude/skills/` → Skill
   - `.claude/agents/` → Agent

2. **Parse frontmatter** between `---` delimiters

3. **Analyze body** for tool usage, workflow patterns

### Step 3: Present Current State

Show a summary:

```markdown
## Current State: {component_name}

**Type:** {Command | Skill | Agent}
**Location:** {file_path}

### Current Frontmatter
```yaml
{current frontmatter}
```

### Configuration Checklist
- [x] name: valid format
- [x] description: present
- [ ] allowed-tools: not configured
- [ ] hooks: no validation hooks
```

### Step 4: Propose Applicable Patterns

Based on analysis, propose relevant patterns. Use `AskUserQuestion` with multi-select.

See [patterns.md](patterns.md) for full pattern catalog.

### Step 5: Gather Preferences

For each selected pattern, ask targeted questions.

See [analysis-prompts.md](analysis-prompts.md) for questions by pattern.

### Step 6: Generate Recommendations

Show before/after diff with severity:

```markdown
## Recommendations

### Critical
1. **Add missing description triggers**
   - Current: "Handles API operations"
   - Suggested: "Handles API operations including project creation and task management. Use when making API calls or managing backend data."

### Suggested
2. **Add validation hook**
   - Pattern: validation-hook
   - Checks: CONVEX_HTTP_URL, .claude/project.json exists

### Optional
3. **Restrict tools**
   - Current: all tools
   - Suggested: Read, Grep, Glob, Bash
```

### Step 7: Apply Changes

Ask for confirmation, then use `Edit` or `Write` to apply changes.

### Step 8: Continue or Done

Ask if user wants to continue refining or move on.

---

## Quick Pattern Reference

### Command Patterns

| Pattern | When to Suggest |
|---------|-----------------|
| `validation-hook` | Uses Bash |
| `env-check` | Calls APIs |
| `tool-restriction` | Has many tools or no restrictions |
| `arguments-usage` | No $ARGUMENTS in body |

### Skill Patterns

| Pattern | When to Suggest |
|---------|-----------------|
| `context-fork` | Heavy content (>300 lines) |
| `paired-agent` | Has context:fork but no agent |
| `hide-from-menu` | Internal utility |
| `add-triggers` | Vague description |

### Agent Patterns

| Pattern | When to Suggest |
|---------|-----------------|
| `least-privilege` | Has Write/Edit but read-only purpose |
| `skill-injection` | Domain-specific without skills |
| `model-optimization` | Wrong model for task complexity |
| `permission-mode` | Needs auto-approval |

See [patterns.md](patterns.md) for detailed implementations.
