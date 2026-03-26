# Module 2: Security — Knowledge Base

Deep-dive companion for the Security module. Reference this for detailed explanations, external resources, and advanced topics.

---

## What Claude Code Can Access

Claude Code runs as a subprocess in your terminal. It inherits:

- **File system access**: Everything your user account can read in the working directory and below
- **Environment variables**: All variables in your shell session (`env` to list them)
- **Git context**: Full repository history, all branches, remotes
- **Network access**: Can make HTTP requests, download packages, call APIs
- **Process execution**: Can run any command your user can run

### Important: Claude Code does NOT

- Persist data between sessions (beyond files it creates)
- Access files outside the working directory (unless you navigate there)
- Bypass OS-level permissions (runs as your user)
- Remember previous conversations (unless documented in CLAUDE.md)

---

## Common Sensitive File Patterns

| Pattern | What It Contains | Risk |
|---------|-----------------|------|
| `.env`, `.env.local`, `.env.production` | API keys, DB passwords, service URLs | Account compromise |
| `*.pem`, `*.key`, `*.p12` | Cryptographic keys, certificates | Identity theft |
| `credentials.json`, `service-account.json` | Cloud provider credentials | Infrastructure access |
| `~/.ssh/*` | SSH keys | Server access |
| `~/.aws/credentials` | AWS access keys | Cloud resource access |
| `*.sql`, `*.sql.gz` | Database dumps | Data breach |
| `data/`, `fixtures/`, `seeds/` | Test data (may contain real data) | Privacy violation |
| `.npmrc`, `.pypirc` | Package registry tokens | Supply chain attacks |

---

## Permission Modes Deep Dive

### Default Mode (Recommended)

Every tool invocation requires explicit approval:
- File reads: Auto-approved (Read, Glob, Grep)
- File writes: Requires approval (Write, Edit)
- Commands: Requires approval (Bash)
- MCP calls: Requires approval

### Auto-Accept Edits

File operations are auto-approved:
- File reads: Auto-approved
- File writes: **Auto-approved**
- Commands: Still requires approval
- MCP calls: Still requires approval

### Full Auto / YOLO Mode

Everything is auto-approved:
- **Use only in disposable environments**
- CI/CD containers, throwaway branches, sandboxed VMs
- Never on a development machine with access to production systems

### Custom Permission Configuration

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Glob",
      "Grep",
      "Write",
      "Edit"
    ],
    "deny": [
      "rm -rf",
      "git push --force",
      "DROP TABLE",
      "TRUNCATE"
    ]
  }
}
```

---

## .claudeignore Reference

The `.claudeignore` file uses the same syntax as `.gitignore`:

```gitignore
# Secrets and credentials
.env
.env.*
!.env.example
*.pem
*.key
*.p12
credentials.json
service-account*.json

# Private data
data/production/
backups/
*.sql.gz
*.dump

# OS and IDE files
.DS_Store
.idea/
.vscode/settings.json

# Build artifacts with embedded secrets
dist/config.js
```

### Patterns

| Pattern | Matches |
|---------|---------|
| `*.pem` | All `.pem` files in any directory |
| `data/production/` | The entire `data/production/` directory |
| `!.env.example` | Exception — DO include `.env.example` |
| `**/credentials/**` | Any `credentials` directory at any depth |

---

## Security Checklist for New Projects

When starting Claude Code on a new project:

1. [ ] Create `.claudeignore` before first Claude session
2. [ ] Verify `.env` is in `.gitignore`
3. [ ] Create `.env.example` with dummy values
4. [ ] Configure `permissions.deny` in `.claude/settings.json`
5. [ ] Add Security section to `CLAUDE.md`
6. [ ] Work on feature branches, not main
7. [ ] Review all MCP servers before enabling

---

## External Resources

- [Claude Code Permissions Documentation](https://docs.anthropic.com/en/docs/claude-code/security)
- [OWASP Secrets Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html)
- [Git Secrets — Preventing Secrets in Git](https://github.com/awslabs/git-secrets)
- [Claude Code Safety Best Practices](https://docs.anthropic.com/en/docs/claude-code/overview)
