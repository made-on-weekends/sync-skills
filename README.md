# 🥷 Sync Skills

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Standard: SKILL.md](https://img.shields.io/badge/Standard-SKILL.md-success.svg)](https://github.com/agent-skills/skill-md)
[![Language: Bash](https://img.shields.io/badge/Language-Bash%204+-orange.svg)](https://www.gnu.org/software/bash/)

A lightweight, zero-dependency Bash script that symlinks custom developer skills from a single source of truth (`~/.agents/skills`) into all your AI coding assistants' directories. Keep Claude Code, Cursor, Cline, Gemini CLI, and other tools synchronized and up to date without manual copy-pasting.

---

## ⚡ CLI Report & Audit

Sync Skills includes an auditing reporter (`-r` or `--report`) to visualize the status of your skills across all target locations.

```text
$ sync-skills --report

TARGET                                        STATE     CORRECT   STALE   BROKEN  REAL-DIRS
------                                        -----     -------   -----   ------  ---------
/home/user/.claude/skills                     enabled         4       0        0          0
/home/user/.cursor/skills                     enabled         4       0        0          0
/home/user/.gemini/antigravity/skills         enabled         4       0        0          0
/home/user/.windsurf/skills                   DISABLED        -       -        -  (missing)
/home/user/.continue/skills                   DISABLED        -       -        -  (missing)

Report complete. No changes made.
```

---

## ✨ Features

- **🔄 Centralized Source**: Define your skills once in `~/.agents/skills/` and sync them across all tools.
- **🛡️ Safe & Non-Destructive**: Never overwrites existing real directories or custom files in target directories.
- **🔍 State Audit & Reporting**: Check which targets are correctly linked, stale (pointing elsewhere), broken, or missing.
- **🧹 Broken Link Pruning**: Automatically clean up target directories if a skill directory is deleted from the source.
- **🔌 Agent Switch Configuration**: Easily enable/disable specific tools via a declarative configuration mapping.

---

## 🚀 Installation & Setup

1. Copy the script to your home directory (or any directory in your `PATH`):
   ```bash
   cp sync-skills ~/sync-skills
   chmod +x ~/sync-skills
   ```

2. Structure your skills in the canonical source directory:
   ```text
   ~/.agents/skills/
   ├── custom-git-flows/
   │   └── SKILL.md
   └── nextjs-expert/
       └── SKILL.md
   ```
   *Note: Only directories containing a `SKILL.md` file are scanned and synchronized.*

---

## 📖 Usage & CLI Options

Run the script to link your skills:
```bash
~/sync-skills [options]
```

### CLI Options

| Option | Flag | Description |
| :--- | :--- | :--- |
| `--dry-run` | `-n` | Show what changes would be made, without writing anything. |
| `--force` | `-f` | Replace stale symlinks pointing elsewhere (real directories/files remain untouched). |
| `--prune` | `-p` | Detect and remove broken symlinks whose source folder no longer exists. |
| `--report` | `-r` | Run a system audit: prints status tables of links for each target without changing files. |
| `--verbose` | `-v` | Log every action, including skipped checks and no-ops. |
| `--help` | `-h` | Display help instructions and usage context. |

### Advanced: Custom Source Path
To override the source directory temporarily:
```bash
SKILLS_SOURCE=~/my-custom-skills-path ~/sync-skills
```

---

## 📂 Supported Target Directories

The script supports a wide variety of AI-tool skill directories out of the box.

### Default Enabled Targets (STATE = `1`)

| IDE / CLI Tool | Sync Directory Path |
| :--- | :--- |
| **Claude Code** | `~/.claude/skills` |
| **Codex CLI** | `~/.codex/skills` |
| **Cursor** | `~/.cursor/skills` |
| **Cline** | `~/.cline/skills` |
| **GitHub Copilot** | `~/.copilot/skills` |
| **Gemini CLI** | `~/.gemini/skills` |
| **Antigravity** | `~/.gemini/antigravity/skills` |
| **opencode** | `~/.config/opencode/skills` |
| **Hermes** | `~/.hermes/skills` |

### Default Disabled Targets (STATE = `0`)
*Enable these by setting their value to `1` in the configuration block.*

| IDE / CLI Tool | Sync Directory Path |
| :--- | :--- |
| **Windsurf** | `~/.windsurf/skills` |
| **Continue** | `~/.continue/skills` |
| **Roo** | `~/.roo/skills` |
| **Trae** | `~/.trae/skills` |
| **Kiro** | `~/.kiro/skills` |
| **Qoder** | `~/.qoder/skills` |
| **CodeBuddy** | `~/.codebuddy/skills` |

---

## ⚙️ Configuration & Agent Switch

You can enable or disable target directories by editing the `TARGET_STATE` block located at the top of the `sync-skills` script:

```bash
# 0 = disabled (removes target on run) | 1 = enabled (syncs target)
TARGET_STATE["${HOME}/.windsurf/skills"]=0
TARGET_STATE["${HOME}/.claude/skills"]=1
```

> [!WARNING]
> Setting a target to `0` (disabled) tells the script to completely remove the parent directory (`dirname` of the target path, e.g. `~/.windsurf/` or `~/.roo/`), not just the `skills` subdirectory, during synchronization. Ensure you have backed up any configurations in those directories before disabling.

---

## 🐛 Issues & Troubleshooting

Encountered an issue or have a feature suggestion?
- View our [Quick Guide to Reporting Issues](REPORTING.md) before submitting an issue.
- Read [AGENTS.md](AGENTS.md) for quick command reminders and agent coding guidelines.

## 🤝 Contributing

We are always looking for improvements and additions! Please read the [Contribution Guide](CONTRIBUTING.md) to understand our branching strategy, conventions, and pull request checklist.

## ⭐ Support Us

If **Sync Skills** makes your multi-agent workflow seamless and saves you tokens:
- ⭐ **Star this repository** to help others discover the project.
- 📣 **Spread the word** on X/Twitter or your blog.
- ☕ **Support Sync Skills** via [donation](https://asifiqbal.rocks/donation?utm_source=sync_skills&utm_medium=github_readme&utm_campaign=readme&ref=sync-skills-readme) to fund further open-source initiatives.

---

## 📄 License

This project is licensed under the [MIT License](https://choosealicense.com/licenses/mit).
