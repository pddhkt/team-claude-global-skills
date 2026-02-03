---
name: reflect
description: Reviews current session for problems and improves skills/commands/agents. Analyzes API mismatches, workflow issues, and confusion points. Routes stack-specific issues to per-repo reflect skills. Use when encountering errors, wanting to improve prompts, or after a problematic session.
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

Analyze session problems, improve global skills directly, and route stack-specific issues to per-repo reflect skills.

## Contents

- [Quick Start](#quick-start)
- [Interactive Flow](#interactive-flow)
- [Phase References](#phase-references)
- [Plugin Registry](#plugin-registry)
- [Self-Manage Workflow](#self-manage-workflow)
- [Routing to Per-Repo Reflect](#routing-to-per-repo-reflect)
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
    - label: "Browse agents"
      description: "List available agents"
    - label: "Based on analysis"
      description: "Suggest components based on session problems"
```

### Step 3: Route Improvements

After analysis, categorize each improvement by which plugin it belongs to:

1. **Global skills** (git, test, browser, creating-skills, reflect, etc.) → Apply directly to `team-claude-global-skills` repo
2. **Stack-specific skills** → Route to the per-repo reflect skill (see [Plugin Registry](#plugin-registry))
3. **Core workflow skills** (dev-task, planning, agents, etc.) → Route to `/team-claude:reflect`

---

## Phase References

| Phase | Reference | Description |
|-------|-----------|-------------|
| **Session Analysis** | [reference/session-analysis.md](reference/session-analysis.md) | Patterns for identifying problems |
| **Improvement Categories** | [reference/improvement-categories.md](reference/improvement-categories.md) | Types of improvements to suggest |
| **Sync Strategy** | [reference/sync-strategy.md](reference/sync-strategy.md) | Self-manage workflow for global skills repo |

Read the appropriate reference file based on the phase.

---

## Plugin Registry

Static mapping of plugins to their source repos and reflect commands. Use this to route improvements to the correct per-repo reflect skill.

| Plugin | Marketplace ID | Reflect Command |
|--------|---------------|-----------------|
| team-claude-global | team-claude-global-skills | (self — apply directly) |
| team-claude | team-claude-core | `/team-claude:reflect` |
| react-cloudflare-convex | react-cloudflare-convex-skills | `/react-cloudflare-convex:reflect` |
| cdss-microfrontend | cdss-microfrontend-skills | `/cdss-microfrontend:reflect` |
| ctint-backend-go | ctint-backend-go-skills | `/ctint-backend-go:reflect` |
| astro | astro-skills | `/astro:reflect` |
| kotlin-kmp | kotlin-kmp-skills | `/kotlin-kmp:reflect` |

### Routing Flow

After analysis, for each identified improvement:

1. Determine which plugin owns the affected skill/agent
2. If **global** → apply directly (see [Self-Manage Workflow](#self-manage-workflow))
3. If **other plugin** → tell the user which reflect command to run:

```markdown
## Routing Summary

### Apply Now (Global Skills)
- reflect/SKILL.md: Add routing documentation ← will apply directly

### Route to Per-Repo Reflect
- `/team-claude:reflect` — Fix planning skill decomposition pattern
- `/react-cloudflare-convex:reflect` — Update Convex mutation examples

Run these commands to apply stack-specific improvements.
```

---

## Self-Manage Workflow

For improvements to **global skills** (owned by this repo), apply changes directly.

### Locate Source Repository

1. Check `TEAM_CLAUDE_GLOBAL_REPO` environment variable
2. Default to `~/Projects/personal/team-claude-global-skills`
3. Clone from GitHub if not present locally:
   ```bash
   git clone git@github.com:pddhkt/team-claude-global-skills.git
   ```

### Apply Changes

1. Navigate to source repo
2. Edit skill files under `plugins/team-claude-global/skills/`
3. Show diff for user approval
4. Commit with descriptive message:
   ```bash
   git commit -m "reflect: [action] [component]

   Based on session analysis:
   - [problem summary]
   - [improvement summary]"
   ```
5. Push to origin
6. Update plugin: `/plugin marketplace update team-claude-global-skills`

### Sync Options

```
AskUserQuestion:
  question: "Apply changes to global skills repo?"
  header: "Apply"
  options:
    - label: "Yes, commit and push (Recommended)"
      description: "Apply changes, commit, push to origin"
    - label: "Yes, commit only"
      description: "Apply changes and commit, don't push"
    - label: "Preview only"
      description: "Show proposed changes without applying"
```

---

## Problem Categories

### API Mismatches

**Signs:**
- Tool calls returning errors about unknown parameters
- Schema validation failures (e.g., "ArgumentValidationError")
- Type mismatches in function calls
- "does not match validator" errors

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
   - Affected plugin: [plugin name]
   - Suggested fix: [what to change]

2. **Workflow Friction** (Medium)
   - Pattern: [what was repeated]
   - Count: [how many times]
   - Affected plugin: [plugin name]
   - Suggested fix: [improvement]

### Routing

| Plugin | Issues | Reflect Command |
|--------|--------|-----------------|
| team-claude-global | 2 | (apply directly) |
| react-cloudflare-convex | 1 | `/react-cloudflare-convex:reflect` |
```

### Change Preview

Before applying any changes to global skills, show:

```markdown
## Proposed Changes

### File: plugins/team-claude-global/skills/[name]/SKILL.md

\`\`\`diff
- old content
+ new content
\`\`\`

Apply these changes? [Yes/No/Modify]
```

---

## Git Integration

After successful edits to the global skills repo:

```
AskUserQuestion:
  question: "Create git commit in global skills repo?"
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
| Global repo not found | Warn user, show clone instructions |
| No problems detected | Report clean session, offer proactive improvements |
| Conflicting changes | Show diff, let user choose resolution |
| Permission denied | Report error, suggest manual fix |
| Stack plugin not installed | Skip routing, note in summary |

---

## Related Skills

| Skill | Relationship |
|-------|--------------|
| `/creating-skills` | Creates/reviews skill components |
| `/qa` | Quality assurance patterns |
| `/exploration` | Codebase exploration |
| Per-repo reflect | Stack-specific improvement application |
