# PRODUCT.md

> What we're building, for whom, and what is explicitly NOT in scope.
> This is the canonical scope document. Agents must check here before adding features.

## Product

A lightweight, zero-dependency Bash script that scans a local directory of custom developer skills and synchronizes them into enabled AI coding assistant directories (Claude Code, Cursor, Cline, Gemini CLI, etc.) via symlinks.

## Target user

A software engineer who:
- Works with multiple different local AI coding assistants and IDEs (e.g. using Claude Code in CLI, Cursor for IDE, Cline/Roo for VS Code, Gemini/Antigravity plugins).
- Defines custom developer skills (custom guidelines, code styles, workflows) in a central place (`~/.agents/skills`).
- Wants a fast, lightweight, dependency-free utility to keep all their local agent skill directories in sync without manual copy-pasting.

## Anti-persona

- Users looking for a graphical user interface (GUI) or an interactive application.
- Teams looking for a centralized network-based skill synchronization server or cloud backup service (e.g., syncing across multiple developer machines via API/OAuth).
- Users on environments without a POSIX shell or standard Unix utilities (like native Windows Command Prompt/PowerShell without WSL).

## Core value

**Zero dependencies, zero overhead, single command sync.** It provides an instant link state audit and synchronization using only shell builtins and core utilities, making it extremely fast, reliable, and portable across unix-like systems.

## In scope (current phase)

- **Symlink Synchronization:** Scanning a central source (`~/.agents/skills`) and symlinking each subfolder containing `SKILL.md` into target directories.
- **Agent Switch Configuration:** A declarative configuration block (`TARGET_STATE`) to easily toggle synchronization state per tool directory.
- **State Auditing / Reporting:** A read-only report flag (`-r` or `--report`) to audit targets showing correct, stale, broken, or missing links/directories.
- **Broken Link Pruning:** Pruning broken symlinks (`-p` or `--prune`) whose source folders have been deleted.
- **Forced Link Resolution:** Overwriting stale symlinks pointing to outdated paths (`-f` or `--force`).
- **Clean Tool Removal:** Completely wiping target directories when disabled (`TARGET_STATE=0`), keeping user local environments clean of unused tools.

## Explicitly out of scope

- **Non-Symlink Copying:** Copying files/folders directly instead of symlinking. (Symlinks preserve the single-source-of-truth nature).
- **GUI or Menu-driven Configurator:** Any visual tool, dashboard, or tray icon.
- **Cloud/Remote Synchronization:** Syncing with GitHub, S3, or remote servers. (Users should use standard git repositories for syncing `~/.agents/skills` across machines).
- **Interactive Prompts:** The tool must remain non-interactive to facilitate easy scripting, scripting automation, and git hook usage.

## Non-goals

- Not trying to manage the creation or authoring of developer skills.
- Not attempting to validate the structure or correctness of the markdown within `SKILL.md`. It only checks for the existence of `SKILL.md` as a signature.
- Not a replacement for IDE configuration sync tools.

## Success criteria

- Successful synchronization of skills into target folders with standard Unix symlinks.
- Execution speed is instantaneous (<50ms for typical sync runs).
- Dry-run mode (`-n`) matches the actual sync outcomes exactly.
- Link reports accurately display counts of correct, stale, and broken links across all configured assistant directories.
