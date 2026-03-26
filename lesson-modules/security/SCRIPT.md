# Module 2: Security

**Duration**: ~90 minutes
**Prerequisites**: Module 1 (Foundations & Commands) completed

## Chapter Phase Map

| Chapter | Short Title | Phases |
|---------|------------|--------|
| 1 | What Can Claude Code See? | PRESENT → CHECKPOINT |
| 2 | Sensitive Data & Secrets | PRESENT → CHECKPOINT → ACTION → VERIFY |
| 3 | Permission Modes | PRESENT → CHECKPOINT → ACTION → VERIFY |
| 4 | Dangerous Commands | PRESENT → CHECKPOINT |
| 5 | .claudeignore & permissions.deny | PRESENT → CHECKPOINT → ACTION → VERIFY |
| 6 | Safe Workflow Patterns | PRESENT → CHECKPOINT → ACTION → VERIFY |
| 7 | MCP Server Security | PRESENT → CHECKPOINT |
| 8 | Headless Mode Risks | PRESENT → CHECKPOINT |
| 9 | Security Policy | PRESENT → ACTION → VERIFY |
| 10 | Commit & Review | ACTION → VERIFY |

## Chapter Progress Map

```
═══════════════════════════════════════════════════
  Module 2: Security
  10 chapters · ~90 min
═══════════════════════════════════════════════════

   1. What Can Claude Code See?
   2. Sensitive Data & Secrets
   3. Permission Modes
   4. Dangerous Commands
   5. .claudeignore & permissions.deny
   6. Safe Workflow Patterns
   7. MCP Server Security
   8. Headless Mode Risks
   9. Security Policy
  10. Commit & Review

═══════════════════════════════════════════════════
```

---

## Chapter 1: What Can Claude Code See?

### Content

Claude Code operates with **full access** to everything in your project directory. When you start a session, Claude can:

1. **Read all files** in your working directory and subdirectories
2. **Execute shell commands** (with your permission)
3. **Access git history** — commits, branches, diffs
4. **Read environment variables** available in your shell
5. **Access network** — make HTTP requests, install packages, call APIs

This is powerful — but it means anything in your project directory is potentially visible to Claude. This includes:

- `.env` files with API keys and secrets
- Configuration files with database credentials
- Private keys and certificates
- Internal documentation with sensitive business logic
- Customer data in local databases or test fixtures

**Key principle**: Treat Claude Code like a junior developer sitting at your machine. Don't leave anything accessible that you wouldn't want a new team member to see.

### Instructor: Checkpoint

Ask the student:
- "Can you think of any sensitive files in your current repository that Claude Code could read right now?"
- Options: "Yes, I can think of some" / "I'm not sure — help me check" / "My repo is clean"

If they're not sure, help them check:
```bash
# Common sensitive file patterns
ls -la .env* 2>/dev/null
ls -la *.pem *.key 2>/dev/null
ls -la *credentials* *secret* 2>/dev/null
grep -rl "password\|api_key\|secret" --include="*.json" --include="*.yaml" --include="*.yml" -l 2>/dev/null | head -5
```

### Checklist
- [ ] Understand what Claude Code can access in your project
- [ ] Identified any sensitive files in your own repo

---

## Chapter 2: Sensitive Data & Secrets

### Content

**Never pass these directly to Claude Code:**

| Category | Examples | Why It's Dangerous |
|----------|----------|--------------------|
| API Keys & Tokens | `OPENAI_API_KEY`, `STRIPE_SECRET_KEY`, AWS credentials | Could be used to access/charge your accounts |
| Database Credentials | Connection strings, passwords | Direct access to your data |
| Private Keys | SSH keys, SSL certificates, signing keys | Identity theft, man-in-the-middle |
| Personal Data | Customer emails, phone numbers, addresses | Privacy/GDPR violations |
| Internal URLs | Admin panels, staging environments, VPN configs | Exposes internal infrastructure |
| Business Secrets | Pricing algorithms, proprietary logic, unreleased features | Competitive advantage loss |

**Safe patterns for working with secrets:**

1. **Use environment variable references, not values**: Tell Claude "use `process.env.API_KEY`" instead of pasting the actual key
2. **Use placeholder values**: `"password": "YOUR_DB_PASSWORD_HERE"` instead of real passwords
3. **Reference secret managers**: "Fetch the API key from AWS Secrets Manager" instead of hardcoding
4. **Use `.env.example`** with dummy values: Claude can see the structure without the actual secrets

### Instructor: Checkpoint

Ask: "If a colleague asked you to review a PR that adds a `.env` file with real API keys to the repo, what would you say?"
- Options: "I'd reject it immediately" / "I'd ask them to use env vars" / "I'm not sure"

### Instructor: Action

Have the student check their repository for exposed secrets and create a `.env.example` file:

1. Check for any `.env` files that might contain real secrets
2. If they exist, create a `.env.example` with placeholder values
3. Ensure `.env` is in `.gitignore`

### Instructor: Verify

```yaml
verification:
  task_key: audit_secrets
  checks:
    - type: file_contains
      path: .gitignore
      content: ".env"
      message: ".env should be in .gitignore"
    - type: manual_confirm
      question: "Have you verified no real secrets are committed in your repo?"
```

### Checklist
- [ ] Understand what types of data should never be shared with Claude
- [ ] Know safe patterns for referencing secrets
- [ ] Verified your repo doesn't expose secrets
- [ ] Created .env.example with placeholders (if applicable)

---

## Chapter 3: Permission Modes

### Content

Claude Code has 3 permission modes that control what it can do without asking:

| Mode | Behavior | Risk Level |
|------|----------|------------|
| **Default** | Asks permission for file writes, commands, MCP | Low — you approve everything |
| **Auto-accept edits** | File writes without asking, still asks for commands | Medium — typos in CLAUDE.md could cause unwanted edits |
| **YOLO / Full auto** | Everything without asking | **High** — Claude runs any command without confirmation |

**Rules of thumb:**

- **Default mode** for everyday work — safest, you see everything
- **Auto-accept edits** only when you trust the CLAUDE.md is well-configured and you're reviewing diffs
- **YOLO mode** — only for throwaway environments (CI/CD containers, temporary branches). **Never on your main development machine.**

**What can go wrong in YOLO mode:**
- `rm -rf` on wrong directory
- `git push --force` to main
- Installing malicious npm packages
- Overwriting important configuration files
- Running destructive database migrations

### Instructor: Checkpoint

Ask: "Which permission mode would you use for: (a) writing a new feature, (b) running in CI/CD, (c) exploring an unfamiliar codebase?"
- Options: "Default for all three" / "Default, YOLO, Default" / "I want to discuss this"

### Instructor: Action

Have the student inspect their current permission configuration:

```bash
# Check current settings
cat .claude/settings.json 2>/dev/null || echo "No local settings yet"
cat ~/.claude/settings.json 2>/dev/null || echo "No global settings"
```

Have them add an allowlist for safe commands in `.claude/settings.json` if not already configured.

### Instructor: Verify

```yaml
verification:
  task_key: configure_permissions
  checks:
    - type: file_exists
      path: .claude/settings.json
      message: "Settings file should exist with permission configuration"
    - type: manual_confirm
      question: "Can you explain when you would and would NOT use auto-accept mode?"
```

### Checklist
- [ ] Understand the 3 permission modes
- [ ] Know when to use each mode
- [ ] Reviewed your permission settings
- [ ] Configured appropriate allowlists

---

## Chapter 4: Dangerous Commands

### Content

Some commands are especially risky when Claude Code suggests them. Learn to recognize patterns:

**Destructive commands — always review carefully:**

```bash
# File system destruction
rm -rf /                    # Obvious, but rm -rf with any broad path is risky
git clean -fdx              # Removes all untracked files including ignored ones

# Git destruction
git reset --hard            # Loses all uncommitted changes
git push --force            # Overwrites remote history, affects team
git checkout -- .           # Discards all local changes

# Database destruction
DROP TABLE                  # Self-explanatory
TRUNCATE                    # Deletes all data
DELETE FROM ... (no WHERE)  # Deletes all rows

# System-level
chmod -R 777                # Makes everything world-writable
curl ... | bash             # Executes unknown code from internet
npm install <unknown-pkg>   # Could be typosquatting/malicious
```

**Red flags to watch for:**
- Commands with `--force` or `-f` flags
- Commands that pipe to `bash` or `sh`
- Commands that modify system files (`/etc/`, `~/.ssh/`)
- Commands that install packages you didn't request
- Commands that expose ports or create network listeners

**What to do when Claude suggests a dangerous command:**
1. **Read it carefully** before approving
2. **Ask Claude to explain** what the command does and why
3. **Check for safer alternatives** — e.g., `git stash` instead of `git checkout -- .`
4. **Run on a test branch first** if unsure

### Instructor: Checkpoint

Present a set of commands and ask the student to identify which are dangerous:
```
1. git commit -m "fix: update config"
2. rm -rf node_modules && npm install
3. git push --force origin main
4. cat .env
5. chmod 600 ~/.ssh/id_rsa
6. curl https://example.com/install.sh | sudo bash
```

- Options: "3 and 6 are dangerous" / "3, 4, and 6" / "I want to discuss each one"

Correct: #3 (force push to main), #6 (piping unknown URL to bash with sudo). #2 is safe (only deletes node_modules). #4 reads .env but doesn't expose it externally. #5 is actually a good security practice.

### Checklist
- [ ] Can identify destructive commands
- [ ] Know red flags in command suggestions
- [ ] Understand the difference between dangerous and safe-but-scary commands

---

## Chapter 5: .claudeignore & permissions.deny

### Content

Two mechanisms to restrict what Claude Code can access:

**1. `.claudeignore`** — Files Claude should not read

Works like `.gitignore`. Create it in your project root:

```
# Secrets
.env
.env.*
*.pem
*.key
**/credentials/**

# Sensitive data
data/production/
backups/
*.sql.gz

# Personal configs
.claude/settings.local.json
```

**2. `permissions.deny` in `.claude/settings.json`** — Commands Claude cannot run

```json
{
  "permissions": {
    "deny": [
      "rm -rf",
      "git push --force",
      "DROP TABLE",
      "chmod -R 777"
    ]
  }
}
```

**Which to use when:**
- `.claudeignore` → prevent Claude from **reading** sensitive files
- `permissions.deny` → prevent Claude from **running** dangerous commands
- Best practice: use **both** together

### Instructor: Checkpoint

Ask: "What's the difference between .claudeignore and permissions.deny? When would you use each?"
- Options: "claudeignore for files, deny for commands" / "They're the same thing" / "I need more explanation"

### Instructor: Action

Have the student create both:

1. Create `.claudeignore` with patterns relevant to their project
2. Add `permissions.deny` entries to `.claude/settings.json` for dangerous commands

### Instructor: Verify

```yaml
verification:
  task_key: create_security_files
  checks:
    - type: file_exists
      path: .claudeignore
      message: ".claudeignore should exist in project root"
    - type: file_contains
      path: .claudeignore
      content: ".env"
      message: ".claudeignore should exclude .env files"
    - type: file_exists
      path: .claude/settings.json
      message: "Settings file should exist"
    - type: file_contains
      path: .claude/settings.json
      content: "deny"
      message: "Settings should contain permissions.deny"
```

### Checklist
- [ ] Created .claudeignore with relevant patterns
- [ ] Added permissions.deny rules
- [ ] Understand the difference between the two mechanisms

---

## Chapter 6: Safe Workflow Patterns

### Content

Adopt these patterns to work safely with Claude Code:

**1. Branch-first workflow**
```bash
# Always work on a branch, never directly on main
git checkout -b feature/my-changes
# Now it's safe to let Claude make changes
# If anything goes wrong: git checkout main
```

**2. Review before commit**
```bash
# After Claude makes changes, always review
git diff                    # See what changed
git diff --staged           # See what's staged
# Only then: git commit
```

**3. Incremental commits**
- Commit after each logical change, not after a big batch
- Easier to revert specific changes if something goes wrong

**4. Use `.claude/settings.json` allowlists**
```json
{
  "permissions": {
    "allow": [
      "Read",
      "Glob",
      "Grep",
      "Write",
      "Edit"
    ]
  }
}
```
Only allow the tools Claude actually needs for your current task.

**5. Sensitive operations — do them yourself**
- Database migrations on production
- Deployment to production
- Secret rotation
- Access management changes

### Instructor: Checkpoint

Ask: "What's your current workflow when Claude suggests changes? Do you review diffs before committing?"
- Options: "Yes, I always review" / "Sometimes" / "I usually just commit"

### Instructor: Action

Have the student:
1. Create a new branch for the course work (if not already on one)
2. Review their `.claude/settings.json` and add appropriate allowlists
3. Practice the review workflow: let Claude make a small change, then `git diff` before committing

### Instructor: Verify

```yaml
verification:
  task_key: safe_workflow
  checks:
    - type: manual_confirm
      question: "Are you working on a feature branch (not main/master)?"
    - type: file_contains
      path: .claude/settings.json
      content: "allow"
      message: "Settings should have an allowlist configured"
```

### Checklist
- [ ] Working on a feature branch
- [ ] Know the review-before-commit workflow
- [ ] Configured appropriate allowlists
- [ ] Understand which operations to do manually

---

## Chapter 7: MCP Server Security

### Content

MCP (Model Context Protocol) servers extend Claude's capabilities. But each MCP server you add:

1. **Has access to your conversation context**
2. **Can execute actions** on your behalf
3. **May send data externally** if it's an HTTP-based MCP

**Before adding an MCP server, ask:**
- Who created it? Is it from a trusted source?
- What permissions does it need?
- Does it send data to external services?
- Is it open source so you can audit it?

**Safe MCP practices:**
- Only install MCP servers from trusted sources (official Anthropic plugins, verified repos)
- Review MCP server source code before installing
- Use project-level `.mcp.json` (not global) so MCP servers are scoped to the project
- Remove MCP servers you're not actively using

### Instructor: Checkpoint

Ask: "If a colleague shares an MCP server that 'auto-deploys your code to production,' would you install it? What questions would you ask first?"
- Options: "I'd check the source code first" / "I'd ask what it does exactly" / "I'd install it and try"

### Checklist
- [ ] Understand what MCP servers can access
- [ ] Know how to evaluate MCP server trustworthiness
- [ ] Know the difference between project-level and global MCP config

---

## Chapter 8: Headless Mode Risks

### Content

Claude Code can run in **headless mode** (non-interactive) for CI/CD and automation:

```bash
claude -p "Fix all lint errors" --allowedTools Edit,Write,Bash
```

Risks in headless mode:
- **No human review** — Claude executes and commits without your approval
- **Broad tool access** — `--allowedTools` may be too permissive
- **Automated push** — Changes may go directly to remote
- **Secret exposure** — CI environment may have production secrets

**Safe headless mode patterns:**
- Run on **isolated branches** — never on main
- Use **minimal `--allowedTools`** — only what's needed
- **Never pass real secrets** as CLI arguments (they appear in process lists)
- Use **GitHub Actions PR review** — Claude opens PRs, humans merge
- Set **timeouts** to prevent runaway executions
- Review Claude's output **before merging**

### Instructor: Checkpoint

Ask: "If you set up a GitHub Action that uses Claude Code to auto-fix lint errors, what safeguards would you put in place?"
- Options: "Run on PR branches only, require human merge" / "Let it push directly to main" / "I'm not sure"

### Checklist
- [ ] Understand headless mode capabilities and risks
- [ ] Know safe patterns for CI/CD integration
- [ ] Know which tool restrictions to apply

---

## Chapter 9: Security Policy

### Content

Now let's put it all together. Create a **security section** in your `CLAUDE.md` that documents:

1. What Claude should never do in your project
2. What files are off-limits
3. What commands require manual approval
4. Your team's safe workflow patterns

This becomes part of your project's "institutional memory" — every team member who uses Claude Code on this repo will inherit these rules.

### Instructor: Action

Have the student add a `## Security` section to their `CLAUDE.md` with:

1. List of files/directories that contain sensitive data
2. Commands that should never be run automatically
3. Operations that require human approval
4. Team conventions for safe Claude Code usage

Example structure:
```markdown
## Security

### Off-limits files
- `.env*` — contains API keys and secrets
- `data/production/` — contains real customer data
- `*.pem`, `*.key` — cryptographic keys

### Forbidden commands
- `git push --force` — never force-push
- `rm -rf` on any directory outside node_modules/dist/build
- Any `DROP` or `TRUNCATE` SQL commands

### Requires human approval
- Database migrations
- Dependency version upgrades
- Changes to CI/CD pipelines
- Modifications to authentication/authorization code

### Workflow
- Always work on feature branches
- Review all diffs before committing
- Never commit .env files
```

### Instructor: Verify

```yaml
verification:
  task_key: create_security_policy
  checks:
    - type: file_contains
      path: CLAUDE.md
      content: "Security"
      message: "CLAUDE.md should contain a Security section"
    - type: file_quality
      path: CLAUDE.md
      criteria: "Contains security guidelines including off-limits files, forbidden commands, and workflow rules"
      message: "Security section should be comprehensive"
```

### Checklist
- [ ] Added Security section to CLAUDE.md
- [ ] Documented off-limits files
- [ ] Documented forbidden commands
- [ ] Documented approval requirements
- [ ] Documented team workflow conventions

---

## Chapter 10: Commit & Review

### Instructor: Action

Time to commit all security configurations:

1. Review all changes made during this module
2. Stage and commit:
   - `.claudeignore`
   - `.claude/settings.json` (permissions)
   - Updated `CLAUDE.md` (security section)
   - `.env.example` (if created)

### Instructor: Verify

```yaml
verification:
  task_key: commit_security
  checks:
    - type: git_check
      check: "committed"
      message: "All security configurations should be committed"
    - type: manual_confirm
      question: "Have you committed all your security configurations?"
```

### Checklist
- [ ] All security files committed
- [ ] Commit message is descriptive
- [ ] No secrets accidentally committed
