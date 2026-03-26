# Claude Code Developer Course

> **Learn Claude Code by DOING Claude Code** — An interactive course where teams learn by building real configurations for their own repositories.

## What is This?

A Claude Code plugin that provides a **6-module interactive course** teaching how to use Claude Code effectively. Unlike traditional tutorials:

- **You work on YOUR repository** — not toy examples
- **Claude teaches you inside Claude Code** — meta-learning!
- **Validators check your work** — know when you're done
- **Role-specific guidance** — adapts to your job function and tech stack

## Installation

### Option 1: Plugin Install (Recommended)

```bash
claude plugin marketplace add https://github.com/vprkhdk/cc-course-marketplace
claude plugin install cc-course@cc-course
```

### Option 2: Manual Installation

```bash
git clone https://github.com/vprkhdk/cc-course.git ~/.claude/plugins/cc-course
```

### Option 3: Development Mode

```bash
git clone https://github.com/vprkhdk/cc-course.git
claude --plugin-dir ./cc-course
```

## Getting Started

```bash
# 1. Install the plugin (see above)

# 2. Start Claude Code in your project directory
cd /path/to/your/repo
claude

# 3. Install the MCP server (one-time setup)
/cc-course:setup

# 4. Start the course!
/cc-course:start 1
```

That's it! Claude will guide you from there.

## Course Structure

6 modules, ~11 hours total. See [curriculum/modules.md](curriculum/modules.md) for the full registry.

| # | Module | Duration | What You'll Build |
|---|--------|----------|-------------------|
| 1 | **Foundations & Commands** | 120 min | CLAUDE.md, custom slash command, permission config |
| 2 | **Security** | 90 min | Security policy, .claudeignore, permissions.deny, safe workflow patterns |
| 3 | **Skills** | 90 min | Custom skills in `.claude/skills/` |
| 4 | **Extensions** | 120 min | Hooks, MCP config, advanced commands |
| 5 | **Agents** | 120 min | Multi-agent patterns, git worktrees (`claude -w`) |
| 6 | **Workflows** | 120 min | GitHub Actions, automation scripts, `/schedule` |

## Supported Roles

The course adapts examples, skills, hooks, and workflows to your role. See [curriculum/roles.md](curriculum/roles.md) for full details.

| Role | Tech Stack |
|------|-----------|
| **Frontend** | React, Next.js, TypeScript, Tailwind |
| **Backend** | NestJS, TypeScript, Drizzle |
| **QA** | Playwright, Jest, E2E testing |
| **DevOps** | Terraform, Docker, K8s, GitHub Actions |
| **Marketing** | Performance marketing — with sub-specializations: creative, UAM, creative producer, PMM |
| **Mobile** | iOS (Swift/SwiftUI) or Android (Kotlin/Compose) — platform chosen at start |

## Commands

| Command | Description |
|---------|-------------|
| `/cc-course:setup` | Install the required MCP server (run once) |
| `/cc-course:start 1`–`6` | Begin a specific module |
| `/cc-course:continue` | Signal you're ready for the next step |
| `/cc-course:status` | See your progress |
| `/cc-course:validate` | Check if current module is complete |
| `/cc-course:hint` | Get help with current task |
| `/cc-course:submit` | Package completed work for review |

## Teaching Modes

Choose how Claude teaches you:

| Mode | Style |
|------|-------|
| **Sensei** | Strict — never does the work for you, breaks tasks into micro-steps |
| **Coach** (default) | Balanced — guides you, helps when stuck |
| **Copilot** | Hands-on — demonstrates alongside you, you make modifications |

## Prerequisites

- Claude Code installed ([installation guide](lesson-modules/foundations-and-commands/installation-guide.md))
- Claude Pro, Max, Teams, or Enterprise subscription
- A repository you can work on (real project preferred)
- ~11 hours over 1-4 weeks

## What You'll Have When Done

```
your-repo/
├── CLAUDE.md                    # Project context with security section
├── .claudeignore                # Files hidden from Claude
├── .mcp.json                    # MCP server configurations
├── .claude/
│   ├── settings.json            # Permissions, hooks, deny rules
│   ├── skills/                  # Custom team standards and procedures
│   └── commands/                # Custom slash commands
├── .github/workflows/
│   └── claude-*.yml             # CI/CD with Claude reviews
└── scripts/
    └── claude-*.sh              # Headless automation
```

## Project Structure

```
cc-course/
├── curriculum/
│   ├── modules.md               # Module registry (single source of truth)
│   └── roles.md                 # Role registry (single source of truth)
├── lesson-modules/
│   ├── foundations-and-commands/ # Module 1
│   ├── security/                # Module 2
│   ├── skills/                  # Module 3
│   ├── extensions/              # Module 4
│   ├── agents/                  # Module 5
│   └── workflows/               # Module 6
├── skills/                      # Course commands (/cc-course:*)
├── agents/                      # AI reviewer agent
├── mcp/cclogviewer/             # Bundled MCP server for session tracking
├── progress.json                # Student progress template
└── CLAUDE.md                    # Plugin instructions for Claude
```

Module order is defined in `curriculum/modules.md`, not by directory naming.

## Adding Modules & Roles

The course uses central registries so extending it is simple:

**Add a module:**
1. Create directory in `lesson-modules/` with `SCRIPT.md` + `KNOWLEDGE.md`
2. Add row to `curriculum/modules.md`
3. Add entry to `progress.json`

**Add a role:**
1. Add row to `curriculum/roles.md`

All teaching files reference these registries — no need to update 10+ files.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Commands not found | Restart Claude Code, verify `~/.claude/plugins/cc-course/skills/` exists |
| MCP server not starting | Run `/cc-course:setup` or install manually (see [installation guide](lesson-modules/foundations-and-commands/installation-guide.md)) |
| Plugin not loading | Run `claude --debug` and check for plugin messages |

## License

MIT License — use freely, attribution appreciated.
