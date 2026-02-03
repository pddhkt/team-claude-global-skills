# Self-Manage & Routing Strategy

How the global reflect skill manages its own repo and routes to per-repo reflect skills.

---

## Architecture

The reflect system uses a hybrid approach:

- **Global reflect** (this skill) handles session analysis and manages the `team-claude-global-skills` repo directly
- **Per-repo reflect** skills handle applying changes to their specific repos

```
Global Reflect
  ├── Analyze session (universal logic)
  ├── Categorize improvements
  ├── Self-apply → team-claude-global-skills repo
  └── Route → per-repo reflect commands
```

---

## Source Repository Location

### Detection

```bash
# 1. Check environment variable
REPO="${TEAM_CLAUDE_GLOBAL_REPO:-}"

# 2. Fall back to default path
if [[ -z "$REPO" || ! -d "$REPO" ]]; then
  REPO="$HOME/Projects/personal/team-claude-global-skills"
fi

# 3. Verify it's a git repo with the expected structure
if [[ -d "$REPO/.git" && -d "$REPO/plugins/team-claude-global" ]]; then
  echo "Global skills repo found at: $REPO"
else
  echo "Global skills repo not found"
fi
```

### If Not Found

```markdown
## Global Skills Repo Not Found

Expected at: ~/Projects/personal/team-claude-global-skills

To set up:
1. Clone: `git clone git@github.com:pddhkt/team-claude-global-skills.git ~/Projects/personal/team-claude-global-skills`
2. Or set env var: `export TEAM_CLAUDE_GLOBAL_REPO="/your/path"`

Global skill improvements will be skipped. Per-repo routing still works.
```

---

## Self-Apply Workflow

For changes to skills owned by `team-claude-global-skills`:

### 1. Edit Files

All global skills live under:
```
plugins/team-claude-global/skills/
├── agent-browser/
├── creating-skills/
├── git/
├── install-v2/
├── reflect/
├── smart-commit/
└── test/
```

### 2. Show Diff

Before committing, display proposed changes:

```bash
cd "$REPO"
git diff --stat
git diff
```

### 3. Commit

```bash
cd "$REPO"
git add plugins/team-claude-global/skills/
git commit -m "reflect: [action] [component]

Based on session analysis:
- [problem summary]
- [improvement summary]"
```

### 4. Push

```bash
git push origin main
```

### 5. Update Plugin

```
/plugin marketplace update team-claude-global-skills
```

---

## Routing to Per-Repo Reflect

When analysis identifies improvements for other plugins, route them.

### Plugin Registry

| Plugin | Env Var | Default Path | Reflect Command |
|--------|---------|-------------|-----------------|
| team-claude | `TEAM_CLAUDE_CORE_REPO` | `~/Projects/personal/team-claude-core` | `/team-claude:reflect` |
| react-cloudflare-convex | `REACT_CLOUDFLARE_CONVEX_SKILLS_REPO` | `~/Projects/personal/react-cloudflare-convex-skills` | `/react-cloudflare-convex:reflect` |
| cdss-microfrontend | `CDSS_SKILLS_REPO` | `~/Projects/personal/cdss-microfrontend-skills` | `/cdss-microfrontend:reflect` |
| ctint-backend-go | `CTINT_BACKEND_GO_SKILLS_REPO` | `~/Projects/personal/ctint-backend-go-skills` | `/ctint-backend-go:reflect` |
| astro | `ASTRO_SKILLS_REPO` | `~/Projects/personal/astro-skills` | `/astro:reflect` |
| kotlin-kmp | `KOTLIN_KMP_SKILLS_REPO` | `~/Projects/personal/kotlin-kmp-skills` | `/kotlin-kmp:reflect` |

### Routing Output

After analysis, present a routing summary:

```markdown
## Improvement Routing

### Global Skills (apply now)
1. **reflect/SKILL.md** — Add missing routing documentation
2. **git/SKILL.md** — Update commit message format

### Stack-Specific (run per-repo reflect)
3. **`/react-cloudflare-convex:reflect`** — Fix Convex query pattern in backend skill
4. **`/team-claude:reflect`** — Update planner agent model assignment

Run the per-repo reflect commands to apply stack-specific improvements.
```

---

## Conflict Handling

### Plugin Not Installed

If a routed plugin isn't installed in the current project:

```markdown
Note: The following improvements target plugins not installed in this project:
- kotlin-kmp: 1 improvement identified

These will be noted for when you next work in a project using that stack.
```

### Multiple Plugins Affected

When improvements span multiple plugins, process them in order:

1. Apply global improvements first (self-manage)
2. List per-repo commands for the user to run
3. Each per-repo reflect handles its own commit/push cycle

---

## Verification

After applying global changes:

```bash
# Verify changes were committed
cd "$REPO"
git log --oneline -1

# Verify plugin update
# User runs: /plugin marketplace update team-claude-global-skills
```
