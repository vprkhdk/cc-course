# Module Registry

Single source of truth for all course modules. **All other files must reference this registry** instead of hardcoding module lists.

## Module Order

Modules are completed sequentially. Each module unlocks when the previous one is validated.

| # | Directory | Title | Duration | What You'll Build |
|---|-----------|-------|----------|-------------------|
| 1 | `foundations-and-commands` | Foundations & Commands | 120 min | CLAUDE.md, custom slash command |
| 2 | `security` | Security | 90 min | Security policy, .claudeignore, permissions.deny, safe workflow patterns |
| 3 | `skills` | Skills | 90 min | Custom skills in `.claude/skills/` |
| 4 | `extensions` | Extensions | 120 min | Hooks, MCP config, advanced commands |
| 5 | `agents` | Agents | 120 min | Multi-agent patterns, git worktrees |
| 6 | `workflows` | Workflows | 120 min | GitHub Actions, automation scripts |

## Module Directory Naming

Directories live in `lesson-modules/` **without** number prefixes. Order is defined solely by the table above.

```
lesson-modules/
├── foundations-and-commands/
│   ├── SCRIPT.md
│   └── KNOWLEDGE.md
├── security/
│   ├── SCRIPT.md
│   └── KNOWLEDGE.md
├── skills/
│   ├── SCRIPT.md
│   └── KNOWLEDGE.md
├── extensions/
│   ├── SCRIPT.md
│   └── KNOWLEDGE.md
├── agents/
│   ├── SCRIPT.md
│   └── KNOWLEDGE.md
└── workflows/
    ├── SCRIPT.md
    └── KNOWLEDGE.md
```

## How to Add a New Module

1. Create directory in `lesson-modules/{name}/` with `SCRIPT.md` and `KNOWLEDGE.md`
2. Add a row to the **Module Order** table above (insert at desired position)
3. Add module entry to `progress.json` template (with tasks from SCRIPT.md)
4. Add schema migration in `skills/migration.md`

That's it — all other files read from this registry.

## progress.json Module Keys

Module keys in `progress.json` use the **directory name** (not the number). Example: `"security"`, not `"2-security"`.

## Unlocking Logic

Linear chain: module N unlocks when module N-1 is validated. First module is always available.
