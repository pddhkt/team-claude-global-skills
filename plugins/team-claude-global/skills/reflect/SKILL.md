---
name: reflect
description: Reviews current session for problems and improves skills/commands/agents. Analyzes API mismatches, workflow issues, and confusion points. Use when encountering errors, wanting to improve prompts, or after a problematic session.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
  - AskUserQuestion
---

# Reflect Skill

Analyze session problems and improve Claude Code configurations with dual-sync to the team-claude-plugin repo.

## Contents

- [Quick Start](#quick-start)
- [Interactive Flow](#interactive-flow)
- [Phase References](#phase-references)
- [Dual-Sync Mechanism](#dual-sync-mechanism)
- [Problem Categories](#problem-categories)

---

## Quick Start

```bash
/reflect                              # Starts interactive session
/reflect "API mismatch in backend"    # Optional: provide focus area
```

---

## Interactive Flow

### Step 1: Mode Selection

```
AskUserQuestion:
  question: "What would you like to do?"
  header: "Mode"
  options:
    - label: "Analyze Session"
      description: "Review current conversation for problems and patterns"
    - label: "Improve Component"
      description: "Enhance a specific skill/command/agent"
    - label: "Full Cycle"
      description: "Analyze session then apply improvements"
```

### Step 2a: Analyze Mode - Scope Selection

```
AskUserQuestion:
  question: "What scope should I analyze?"
  header: "Scope"
  options:
    - label: "Recent errors"
      description: "Focus on tool failures and error messages"
    - label: "Workflow friction"
      description: "Find points of confusion or repeated attempts"
    - label: "Full session"
      description: "Comprehensive analysis of entire conversation"
```

### Step 2b: Improve Mode - Target Selection

```
AskUserQuestion:
  question: "Which component do you want to improve?"
  header: "Target"
  options:
    - label: "Browse skills"
      description: "List available skills to choose from"
    - label: "Browse commands"
      description: "List available commands"
    - label: "Browse agents"
      description: "List available agents"
    - label: "Based on analysis"
      description: "Suggest components based on session problems"
```

---

## Phase References

| Phase | Reference | Description |
|-------|-----------|-------------|
| **Session Analysis** | [reference/session-analysis.md](reference/session-analysis.md) | Patterns for identifying problems |
| **Improvement Categories** | [reference/improvement-categories.md](reference/improvement-categories.md) | Types of improvements to suggest |
| **Sync Strategy** | [reference/sync-strategy.md](reference/sync-strategy.md) | Dual-sync mechanism |

Read the appropriate reference file based on the phase.

---

## Dual-Sync Mechanism

When making changes to skills/commands/agents, changes sync to BOTH locations:

### Locations

| Type | User Location | Plugin Repo Location |
|------|---------------|----------------------|
| Global skills | `~/.claude/skills/{name}/` | `team-claude-plugin/global/skills/{name}/` |
| Core skills | `.claude/skills/{name}/` | `team-claude-plugin/core/skills/{name}/` |

### Plugin Repo Path

```bash
PLUGIN_REPO="/home/lmt/Projects/personal/team-claude-plugin"
```

### Sync Flow

1. **Detect locations** - Check if plugin repo exists
2. **Compare files** - Show diff if files differ between locations
3. **User choice** - Ask where to apply changes
4. **Apply changes** - Update selected locations
5. **Optional git commit** - Commit to plugin repo if requested

### Sync Options

```
AskUserQuestion:
  question: "Where should I apply changes?"
  header: "Sync"
  options:
    - label: "Both locations (Recommended)"
      description: "Update ~/.claude and plugin repo"
    - label: "User config only"
      description: "Update ~/.claude/skills/ only"
    - label: "Plugin repo only"
      description: "Update team-claude-plugin only"
```

---

## Problem Categories

### API Mismatches

**Signs:**
- Tool calls returning errors about unknown parameters
- Schema validation failures (e.g., "ArgumentValidationError")
- Type mismatches in function calls
- "does not match validator" errors

**Example from session:**
```
Error: Object contains extra field `dependencies` that is not in the validator.
```

### Workflow Friction

**Signs:**
- Repeated attempts at same task
- Multiple file reads for same information
- Backtracking phrases: "actually", "let me try", "on second thought"
- Same glob/grep patterns repeated

### Context Gaps

**Signs:**
- Information exists in skills but Claude didn't know
- Conventions rediscovered through trial/error
- Questions about patterns documented elsewhere

### Tool Failures

**Signs:**
- Bash commands failing with unexpected errors
- File operations rejected
- Permission errors

---

## Output Formats

### Analysis Report

```markdown
## Session Analysis Report

### Problems Found

1. **API Mismatch** (Critical)
   - Location: [where in conversation]
   - Issue: [specific error]
   - Affected component: [skill/command name]
   - Suggested fix: [what to change]

2. **Workflow Friction** (Medium)
   - Pattern: [what was repeated]
   - Count: [how many times]
   - Suggested fix: [improvement]

### Improvement Suggestions

| Component | Change | Priority |
|-----------|--------|----------|
| [name] | [description] | High/Medium/Low |
```

### Change Preview

Before applying any changes, show:

```markdown
## Proposed Changes

### File: [path]

\`\`\`diff
- old content
+ new content
\`\`\`

### Sync Status

| Location | Action |
|----------|--------|
| ~/.claude/skills/[name]/ | Will update |
| team-claude-plugin/global/skills/[name]/ | Will update |

Apply these changes? [Yes/No/Modify]
```

---

## Git Integration

After successful edits to plugin repo:

```
AskUserQuestion:
  question: "Create git commit in plugin repo?"
  header: "Git"
  options:
    - label: "Yes"
      description: "Commit changes with descriptive message"
    - label: "No"
      description: "Leave changes uncommitted"
```

Commit format:
```
reflect: [action] [component]

Based on session analysis:
- [problem summary]
- [improvement summary]
```

---

## Error Handling

| Scenario | Action |
|----------|--------|
| Plugin repo not found | Sync to ~/.claude only, warn user |
| No problems detected | Report clean session, offer proactive improvements |
| Conflicting changes | Show diff, let user choose resolution |
| Permission denied | Report error, suggest manual fix |

---

## Related Skills

| Skill | Relationship |
|-------|--------------|
| `/skills` | Creates/reviews components (creating-skills) |
| `/qa` | Quality assurance patterns |
| `/exploration` | Codebase exploration |
