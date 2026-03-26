# Migration System

Schema versioning and migration logic for progress.json.

When the plugin evolves (new modules, renamed fields, new tasks), students who already started the course need their `progress.json` updated without losing progress. Migrations handle this automatically.

## Current Schema Version

```
CURRENT_VERSION = "1.0"
```

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-03-25 | Initial version. 6 modules (foundations-and-commands, security, skills, extensions, agents, workflows). Module keys use directory names without number prefixes. |

---

## Migration Check Flow

Run this check on every `/cc-course:start`:

```
/cc-course:start N
        │
        ▼
Read student progress.json
        │
        ▼
Get schema_version (default "1.0" if missing)
        │
        ▼
Compare with CURRENT_VERSION
        │
        ├── Same version → Continue normally
        │
        ├── Older version → Run migrations sequentially
        │
        └── Newer version → Warn user to update plugin
```

---

## How Migrations Work

Migrations are **not executable code** — they are instructions for Claude to follow when transforming a student's progress.json. Claude reads the migration description and applies the changes step by step.

### Key Principles

1. **Additive by default**: New fields get default values, existing fields preserved
2. **Never delete user data**: Even if a task is removed, keep the old completion status
3. **Sequential**: Always migrate through each version step (2.0 → 2.1 → 3.0, not 2.0 → 3.0)
4. **Idempotent**: Running the same migration twice produces same result
5. **Backup first**: Create `progress.json.backup` before any migration

### Version Comparison

```python
def compare_versions(student_version, plugin_version):
    """
    Compare semantic versions.
    Returns: -1 (older), 0 (same), 1 (newer)
    """
    student_parts = [int(x) for x in student_version.split(".")]
    plugin_parts = [int(x) for x in plugin_version.split(".")]

    for s, p in zip(student_parts, plugin_parts):
        if s < p:
            return -1  # student is older → run migrations
        if s > p:
            return 1   # student is newer → warn to update plugin

    if len(student_parts) < len(plugin_parts):
        return -1
    if len(student_parts) > len(plugin_parts):
        return 1

    return 0  # same version → no migration needed
```

### Sequential Execution

```python
VERSION_ORDER = ["1.0"]  # Add new versions here as they are released

MIGRATIONS = {
    # "2.0 → 2.1": migrate_2_0_to_2_1,
    # Add migrations as versions are released
}

def run_migrations(progress, from_version, to_version):
    start_idx = VERSION_ORDER.index(from_version)
    end_idx = VERSION_ORDER.index(to_version)

    for i in range(start_idx, end_idx):
        from_v = VERSION_ORDER[i]
        to_v = VERSION_ORDER[i + 1]
        migration_key = f"{from_v} → {to_v}"

        if migration_key in MIGRATIONS:
            progress = MIGRATIONS[migration_key](progress)

    progress["schema_version"] = to_version
    return progress
```

---

## Migration Templates

Use these as starting points when you need to write a migration.

### Template: Adding a New Module

When a new module is added to the course mid-way through students taking it:

```python
def migrate_X_to_Y(progress):
    """
    Add {module_name} module.

    Changes:
    - Add new "{module_key}" module entry to progress.modules
    - Set status based on prerequisite module completion
    """
    if "{module_key}" not in progress["modules"]:
        # Check if the prerequisite module is completed
        prereq = progress["modules"].get("{prereq_key}", {})
        status = "unlocked" if prereq.get("status") == "completed" else "locked"

        progress["modules"]["{module_key}"] = {
            "status": status,
            "started_at": None,
            "completed_at": None,
            "sessions": [],
            "tasks": {
                "task_1": False,
                "task_2": False,
                # ... add all tasks from the module's SCRIPT.md
            },
            "submission": None,
        }

    # Rebuild modules dict in correct order (read from curriculum/modules.md)
    module_order = ["foundations-and-commands", "security", "skills", ...]
    ordered = {k: progress["modules"][k] for k in module_order if k in progress["modules"]}
    progress["modules"] = ordered

    return progress
```

### Template: Adding a New Task to Existing Module

When a chapter is added to an existing module:

```python
def migrate_X_to_Y(progress):
    """
    Add {task_name} task to {module_name} module.

    Changes:
    - Add "{task_key}" task (default: false) to {module_key}.tasks
    """
    module = progress["modules"].get("{module_key}", {})
    if "tasks" in module and "{task_key}" not in module["tasks"]:
        module["tasks"]["{task_key}"] = False

    return progress
```

### Template: Adding a New Field to Student

When new student metadata is tracked:

```python
def migrate_X_to_Y(progress):
    """
    Add {field_name} field to student.

    Changes:
    - Add student.{field_name} (default: null)
    """
    if "{field_name}" not in progress.get("student", {}):
        progress["student"]["{field_name}"] = None

    return progress
```

### Template: Renaming Module Keys

When module directory names change:

```python
def migrate_X_to_Y(progress):
    """
    Rename module keys.

    Changes:
    - Rename "{old_key}" → "{new_key}" in progress.modules
    """
    key_mapping = {
        "{old_key}": "{new_key}",
    }

    new_modules = {}
    for old_key, data in progress["modules"].items():
        new_key = key_mapping.get(old_key, old_key)
        new_modules[new_key] = data

    progress["modules"] = new_modules

    # Update current_module reference if needed
    if progress.get("current_module") in key_mapping:
        progress["current_module"] = key_mapping[progress["current_module"]]

    return progress
```

---

## Migration Messages

### Success

```
Welcome back! The course plugin has been updated.

Migrating your progress from v{old} to v{new}...
✓ Migration {old} → {new}: {description}

Your progress is preserved. Continue with /cc-course:start {current_module}
```

### Newer Than Plugin

```
Warning: Your progress file uses schema v{student_version},
but this plugin only supports up to v{plugin_version}.

Please update the plugin:
  claude plugin update cc-course

Or reinstall:
  claude plugin marketplace add https://github.com/vprkhdk/cc-course-marketplace
  claude plugin install cc-course@cc-course
```

### Migration Failure

```
Migration failed during {from} → {to}

Error: {error_message}

Your original progress has been backed up to:
  {student-repo}/.claude/claude-course/progress.json.backup

Options:
1. Restore backup: cp progress.json.backup progress.json
2. Start fresh: delete progress.json and run /cc-course:start 1
3. Report issue: https://github.com/vprkhdk/cc-course/issues
```

---

## Backup Strategy

Before any migration:
1. Copy `progress.json` → `progress.json.backup`
2. Run migration on the original
3. On failure: restore from backup

Recommend students commit progress before plugin updates:
```bash
git add .claude/claude-course/progress.json
git commit -m "Backup course progress before plugin update"
```

---

## Adding a New Migration (Checklist)

When releasing a plugin version with schema changes:

1. Increment `CURRENT_VERSION` at the top of this file
2. Add row to **Version History** table
3. Add version to `VERSION_ORDER` list
4. Write migration function using a template above
5. Register in `MIGRATIONS` dict
6. Update `progress.json` template in plugin root
7. Test: create a progress.json with old version, run `/cc-course:start`, verify migration
