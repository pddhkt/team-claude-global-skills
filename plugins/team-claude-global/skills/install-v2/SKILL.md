---
name: install-v2
description: Install the Team Claude plugin with local Convex backend to any project. Use when setting up a new project with Team Claude, explaining the installation process, or troubleshooting installation issues.
---

# Team Claude Plugin Installation v2

Install the Team Claude plugin with Convex backend to any project.

## Instructions

When the user invokes `/install-v2`, follow these steps:

### Step 1: Determine target path

- If user provides a path: use that path
- Otherwise: use current working directory

### Step 2: Run the installer

Run this exact command (replace `<target-path>` with the actual path):

```bash
printf '0\nn\n' | ~/.claude/scripts/install.sh <target-path>
```

This command:
- Selects "Core only" (option 0) - works for any project
- Skips Convex initialization prompt (we'll do it manually if needed)
- Installs all core workflow skills + Convex backend automatically

**Note:** Global skills (agent-browser, git, smart-commit, test, creating-skills) should already be in `~/.claude/skills/` if you ran `~/.claude/scripts/install.sh --global`.

### Step 3: Verify installation

Check that these were created:
- `<target-path>/.claude/` directory with commands, skills, agents, scripts, hooks
- `<target-path>/convex/` directory with schema.ts, http.ts, etc.
- `<target-path>/.env.local` with Convex configuration

### Step 4: Report to user

Tell the user:

**Installation complete!**

Next steps:
1. `npm install` - Install dependencies (includes convex)
2. `npx convex dev` - Start local Convex backend
3. `/init-project` - Initialize project configuration
4. `/init-phase "description"` - Create your first phase with tasks

## What Gets Installed

```
target-project/
├── .claude/
│   ├── commands/      # Command definitions (dev-loop, dev-task, etc.)
│   ├── agents/        # Agent definitions
│   ├── skills/        # Skill definitions (flattened from categories)
│   ├── scripts/       # Helper scripts
│   ├── hooks/         # Automation hooks
│   ├── settings.json  # Claude Code settings
│   └── settings.local.json  # Local hooks config
├── convex/            # Convex backend
│   ├── schema.ts      # Database schema
│   ├── http.ts        # HTTP API router
│   ├── phases.ts      # Phases CRUD
│   ├── tasks.ts       # Tasks CRUD
│   ├── subtasks.ts    # Subtasks operations
│   ├── loopSessions.ts # Dev-loop tracking
│   └── counters.ts    # ID generation
└── .env.local         # Environment config
```

## Skills Organization

Skills are organized into two locations:

### Global Skills (`~/.claude/skills/`)
Universal skills available across ALL projects. Install with `~/.claude/scripts/install.sh --global`:
- `agent-browser` - Browser automation CLI
- `git` - Git commit conventions
- `smart-commit` - Group git changes into commits
- `test` - Generate tests
- `creating-skills` - Skill authoring guide
- `install-v2` - This installation skill

### Project Skills (installed to `.claude/skills/`)
Workflow skills for Team Claude task management, installed per-project:
- `api` - Task Manager API docs
- `discover` - Feature discovery
- `exploration` - Codebase exploration for tasks
- `planning` - Task planning
- `init-project` - Initialize project configuration
- `project-setup` - Configure .claude folder
- `qa` - Quality assurance (review/fix)

## Architecture

### Data Storage Split

```
LOCAL FILES                      CONVEX DATABASE
─────────────────────────────    ─────────────────────────────
.claude/project.json             phases (milestones)
  - projectId                    tasks (FT-001, BF-001, etc.)
  - name, description            subtasks (execution items)
  - specs.techstack              loopSessions (dev-loop tracking)
  - specs.features               counters (ID generation)
```

**Why this split?**
- Project config rarely changes → stored locally in repo
- Tasks/subtasks change frequently → stored in Convex for real-time sync

### Convex Backend

The local Convex backend runs at `http://localhost:3210` and provides:

| Table | Purpose |
|-------|---------|
| `phases` | Milestones containing grouped tasks |
| `tasks` | Individual work items (FT-001, BF-001, etc.) |
| `subtasks` | Execution items within tasks |
| `loopSessions` | Dev-loop session tracking |
| `counters` | Sequential ID generation |

### Environment Configuration

Configure `.env.local`:

```bash
# Local development:
#   - Backend: localhost:3210
#   - HTTP actions (site-proxy): localhost:3211

CONVEX_SITE_URL=http://localhost:3210
CONVEX_HTTP_URL=http://localhost:3211
```

## Troubleshooting

### Convex Not Starting

1. Check `package.json` has convex dependency
2. Run `npm install` or `pnpm install`
3. Check no other process on port 3210
4. Check `convex/` folder exists with schema.ts

### API Connection Failed

1. Verify `CONVEX_HTTP_URL=http://localhost:3211` in `.env.local`
2. Check Convex dev server is running
3. Test health endpoint: `curl http://localhost:3211/health`

### Project Not Found

If you get "projectId required" errors:
1. Run `/init-project` to create `.claude/project.json`
2. Or manually create the file with a valid projectId
