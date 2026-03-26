# Claude Code and Course Installation Guide

**Complete this guide before starting the interactive course.**

---

## Prerequisites

- **Operating system**: macOS 13.0+, Windows 10 1809+, Ubuntu 20.04+, Debian 10+, or Alpine 3.19+
- **RAM**: 4 GB+
- **Shell**: Bash, Zsh, PowerShell, or CMD
- **Windows only**: [Git for Windows](https://git-scm.com/downloads/win) is required
- **Account**: Claude Pro, Max, Teams, or Enterprise subscription (free plan does NOT include Claude Code)

---

## Step 1: Install Claude Code

Choose the installation method for your platform:

### Option A: Native Install (Recommended)

Auto-updates in the background — always on the latest version.

**macOS / Linux / WSL:**
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows PowerShell:**
```powershell
irm https://claude.ai/install.ps1 | iex
```

**Windows CMD:**
```batch
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

### Option B: Homebrew (macOS/Linux)

```bash
brew install --cask claude-code
```

> Note: Homebrew does not auto-update. Run `brew upgrade claude-code` periodically.

### Option C: WinGet (Windows)

```powershell
winget install Anthropic.ClaudeCode
```

> Note: WinGet does not auto-update. Run `winget upgrade Anthropic.ClaudeCode` periodically.

### Other options

- **Desktop app**: Download from [macOS](https://claude.ai/api/desktop/darwin/universal/dmg/latest/redirect) or [Windows](https://claude.ai/api/desktop/win32/x64/exe/latest/redirect)
- **VS Code extension**: Search "Claude Code" in Extensions (`Cmd+Shift+X`)
- **Web**: [claude.ai/code](https://claude.ai/code) — no local setup needed

### Troubleshooting Installation

| Issue | Solution |
|-------|----------|
| Command not found after install | Close and reopen your terminal |
| Permission error on macOS/Linux | Check `~/.local/bin` is in your PATH |
| Windows: Git Bash not found | Install [Git for Windows](https://git-scm.com/downloads/win) first |
| Network error | Check internet connection and proxy settings |

> **npm is deprecated.** If you previously installed via `npm install -g @anthropic-ai/claude-code`, migrate to native install and then run `npm uninstall -g @anthropic-ai/claude-code`.

---

## Step 2: Verify Installation

```bash
claude --version
```

For a detailed diagnostic:

```bash
claude doctor
```

**Expected:** Version number and all checks passing.

---

## Step 3: First Launch & Authentication

Navigate to your project directory and start Claude Code:

```bash
cd /path/to/your/project
claude
```

On first launch, Claude Code opens your browser — sign in with your Claude subscription (Pro, Max, Teams, or Enterprise). That's it, no API keys needed.

> **Note:** API keys are only needed for CI/CD and headless automation (covered in Module 6). For everyday use, always log in with your subscription.

---

## Step 4: Verify Authentication

Test that everything works:

```bash
claude "Hello, can you hear me?"
```

**Expected:** Claude responds with a greeting.

If you see an error:
- Ensure you have an active Claude subscription
- Try re-authenticating: `claude auth logout` then `claude`

---

## Step 5: Install the Course Plugin

This course is delivered as a Claude Code plugin via the [cc-course](https://github.com/vprkhdk/cc-course) repository.

1. Add the marketplace:
   ```bash
   claude plugin marketplace add https://github.com/vprkhdk/cc-course-marketplace
   ```
2. Install the plugin:
   ```bash
   claude plugin install cc-course@cc-course
   ```
3. Verify the plugin is installed:
   ```bash
   claude plugin list
   ```
   **Expected:** You should see `cc-course` in the list of installed plugins.

---

## You're Ready!

Once you can:
- Run `claude --version` and see a version number
- Start `claude` and get a response
- Run `claude doctor` with all checks passing
- Course plugin installed

Navigate to your project folder, run the one-time setup, and start the course:

```bash
cd /path/to/your/project
claude

# Inside Claude Code:
/cc-course:setup       # One-time MCP server installation
/cc-course:start 1     # Start Module 1!
```

---

## After Course Completion

When you finish all modules:

1. Run `/cc-course:validate` to verify your work
2. Run `/cc-course:submit` to package your work into a zip archive
3. Send the zip archive to **Vladyslav** for review

The submission includes your configurations, progress data, and session logs.

---

## About This Course

This course teaches you Claude Code by having you **build real configurations in YOUR OWN repository**. Unlike tutorials with toy examples:

- Every task applies to your actual project
- Everything you create (CLAUDE.md, custom commands, skills, hooks) stays in your repo and you keep using it
- Claude teaches you inside Claude Code itself — you learn the tool by using the tool
- Guidance adapts to your role (see [curriculum/roles.md](../../curriculum/roles.md) for all supported roles)

By the end, your repository will have a complete Claude Code setup: project memory, custom commands, skills, hooks, MCP integrations, and CI/CD workflows.

---

## Getting Help

- **Installation issues:** Run `claude doctor`
- **Authentication issues:** Try `claude auth logout` then `claude` again
- **General help:** Visit [code.claude.com/docs](https://code.claude.com/docs/en/overview)

---

*This guide is part of the Claude Code Developer Course.*
