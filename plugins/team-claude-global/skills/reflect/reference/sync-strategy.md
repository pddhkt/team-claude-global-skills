# Dual-Sync Strategy

How to keep ~/.claude and team-claude-plugin repo in sync.

---

## Location Mapping

### Global Skills

| User Location | Plugin Repo Location |
|---------------|----------------------|
| `~/.claude/skills/{name}/` | `team-claude-plugin/global/skills/{name}/` |

### Core/Project Skills

| User Location | Plugin Repo Location |
|---------------|----------------------|
| `.claude/skills/{name}/` | `team-claude-plugin/core/skills/{name}/` |
| `.claude/commands/{name}.md` | `team-claude-plugin/core/commands/{name}.md` |
| `.claude/agents/{name}.md` | `team-claude-plugin/core/agents/{name}.md` |

### Stack-Specific Skills

| User Location | Plugin Repo Location |
|---------------|----------------------|
| `.claude/skills/{name}/` | `team-claude-plugin/stacks/{stack}/skills/{name}/` |

---

## Plugin Repo Detection

```bash
# Default plugin repo location
PLUGIN_REPO="/home/lmt/Projects/personal/team-claude-plugin"

# Check if it exists
if [[ -d "$PLUGIN_REPO" ]]; then
  echo "Plugin repo found at: $PLUGIN_REPO"
else
  echo "Plugin repo not found - will sync to user config only"
fi
```

---

## Sync Decision Tree

```
┌─────────────────────────────────────┐
│ Change requested for component      │
└─────────────────┬───────────────────┘
                  │
                  ▼
┌─────────────────────────────────────┐
│ Is plugin repo available?           │
└─────────┬───────────────┬───────────┘
          │ Yes           │ No
          ▼               ▼
┌─────────────────┐  ┌────────────────┐
│ Compare files   │  │ Update user    │
│ at both         │  │ config only    │
│ locations       │  │ (~/.claude)    │
└────────┬────────┘  └────────────────┘
         │
         ▼
┌─────────────────────────────────────┐
│ Files identical?                    │
└─────────┬───────────────┬───────────┘
          │ Yes           │ No
          ▼               ▼
┌─────────────────┐  ┌────────────────┐
│ Apply to both   │  │ Show diff      │
│ locations       │  │ User decides   │
└─────────────────┘  └────────────────┘
```

---

## File Comparison

### Before Any Edit

Always compare files before making changes:

```bash
# For global skills
USER_FILE="$HOME/.claude/skills/${SKILL_NAME}/SKILL.md"
REPO_FILE="$PLUGIN_REPO/global/skills/${SKILL_NAME}/SKILL.md"

# Check if both exist
if [[ -f "$USER_FILE" && -f "$REPO_FILE" ]]; then
  # Compare
  if diff -q "$USER_FILE" "$REPO_FILE" > /dev/null; then
    echo "FILES_IDENTICAL"
  else
    echo "FILES_DIFFER"
  fi
elif [[ -f "$USER_FILE" ]]; then
  echo "USER_ONLY"
elif [[ -f "$REPO_FILE" ]]; then
  echo "REPO_ONLY"
else
  echo "NEITHER_EXISTS"
fi
```

### Show Diff When Files Differ

```bash
diff --color=always -u "$USER_FILE" "$REPO_FILE" | head -50
```

---

## Conflict Resolution

When files differ between locations:

```
AskUserQuestion:
  question: "Files differ between locations. How to proceed?"
  header: "Conflict"
  options:
    - label: "Show diff first"
      description: "Display differences before deciding"
    - label: "Use user config as base"
      description: "Apply changes to ~/.claude version, then sync to repo"
    - label: "Use plugin repo as base"
      description: "Apply changes to plugin version, then sync to user"
    - label: "Edit both separately"
      description: "I'll handle each location manually"
```

### Resolution Actions

| Choice | Action |
|--------|--------|
| Show diff | Display unified diff, then ask again |
| Use user config | Read ~/.claude file, apply edit, copy to repo |
| Use plugin repo | Read repo file, apply edit, copy to ~/.claude |
| Edit separately | Apply edit to each file independently |

---

## Applying Changes

### To Both Locations (Recommended)

```bash
# 1. Apply edit to user config
edit_file "$USER_FILE" "$OLD_STRING" "$NEW_STRING"

# 2. Copy to plugin repo
cp "$USER_FILE" "$REPO_FILE"

# Or if editing reference files, copy entire directory
cp -r "$HOME/.claude/skills/${SKILL_NAME}/" "$PLUGIN_REPO/global/skills/${SKILL_NAME}/"
```

### To User Config Only

```bash
edit_file "$USER_FILE" "$OLD_STRING" "$NEW_STRING"
echo "Warning: Plugin repo not updated. Run install.sh to sync later."
```

### To Plugin Repo Only

```bash
edit_file "$REPO_FILE" "$OLD_STRING" "$NEW_STRING"
echo "Run 'install.sh --global' to sync to ~/.claude"
```

---

## Git Integration

### After Successful Edit to Plugin Repo

```
AskUserQuestion:
  question: "Create git commit in plugin repo?"
  header: "Git"
  options:
    - label: "Yes"
      description: "Commit: 'reflect: [action] [component]'"
    - label: "No"
      description: "Leave changes uncommitted"
```

### Commit Format

```bash
cd "$PLUGIN_REPO"

# Stage changes
git add "global/skills/${SKILL_NAME}/"

# Commit with descriptive message
git commit -m "reflect: Update ${SKILL_NAME} based on session analysis

Based on session analysis:
- ${PROBLEM_SUMMARY}
- ${IMPROVEMENT_SUMMARY}"
```

### Never Auto-Push

Always leave pushing to the user:
```
Changes committed locally. Run 'git push' when ready to share.
```

---

## Handling Edge Cases

### Component Exists Only in User Config

```
AskUserQuestion:
  question: "This component exists only in ~/.claude. Create in plugin repo?"
  header: "New"
  options:
    - label: "Yes - as global skill"
      description: "Add to team-claude-plugin/global/skills/"
    - label: "Yes - as core skill"
      description: "Add to team-claude-plugin/core/skills/"
    - label: "No"
      description: "Keep only in user config"
```

### Component Exists Only in Plugin Repo

```
AskUserQuestion:
  question: "This component exists only in plugin repo. Install to ~/.claude?"
  header: "Install"
  options:
    - label: "Yes"
      description: "Copy to ~/.claude/skills/"
    - label: "No"
      description: "Edit plugin repo only"
```

### Plugin Repo Not Found

```markdown
## Warning: Plugin Repo Not Found

The team-claude-plugin repository was not found at:
  /home/lmt/Projects/personal/team-claude-plugin

Changes will only be applied to ~/.claude/skills/

To enable dual-sync:
1. Clone team-claude-plugin to the expected location
2. Or update PLUGIN_REPO path in this skill
```

---

## Sync Verification

After applying changes, verify both locations:

```bash
echo "=== Sync Verification ==="

echo "User config:"
ls -la "$USER_FILE" 2>/dev/null || echo "  Not found"

echo "Plugin repo:"
ls -la "$REPO_FILE" 2>/dev/null || echo "  Not found"

echo "Content match:"
if diff -q "$USER_FILE" "$REPO_FILE" > /dev/null 2>&1; then
  echo "  Files are identical"
else
  echo "  Files differ (may need manual sync)"
fi
```
