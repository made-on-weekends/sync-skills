# AGENTS.md

> Briefing packet for AI coding agents (Claude Code, Antigravity, Codex, Cursor, Gemini CLI, etc.).
> Humans should read README.md instead.

## Rules of engagement

These rules are the agent's first read every session. Keep this list short — 5–10 rules, each one absolute.

1. **Do** follow the bash coding conventions documented in this file and `docs/ARCHITECTURE.md`.
2. **Do** run syntax validation via `bash -n sync-skills` before claiming any script changes are complete.
3. **Do** verify any directory removal behavior (`rm -rf`) in dry-run mode first, ensuring parent directory resolutions are safe.
4. **Don't** introduce external dependencies (e.g. `jq`, python, perl) or non-standard shell utilities. Stick to standard POSIX/GNU tools (`find`, `ln`, `rm`, `mkdir`, `readlink`, `dirname`, `basename`).
5. **Don't** bypass `set -euo pipefail` settings. Ensure strict error handling is maintained at all times.
6. **Do** check `docs/DECISIONS.md` before contradicting any documented pattern.
7. **Do** read `docs/SECURITY.md` Hard Rules before touching any code handling path traversal or execution bounds.

## Stack

- Language(s): Bash (GNU Bash 4+)
- Framework(s): None (standalone POSIX-aligned script)
- Dependencies: Pure Bash builtins & coreutils (`find`, `ln`, `rm`, `mkdir`, `readlink`, `dirname`, `basename`)
- Hosting / runtime: Local Linux/macOS environment
- Package/dependency manager: None

## Commands

```bash
# Verify script syntax (linting)
bash -n sync-skills

# Run a dry-run check of skill synchronization
./sync-skills --dry-run

# Run a state audit/report without making changes
./sync-skills --report

# Run standard sync
./sync-skills

# Run sync and force replacement of stale symlinks
./sync-skills --force

# Run sync and prune broken symlinks
./sync-skills --prune

# Run sync with verbose logs
./sync-skills --verbose
```

## Conventions

- **Indentation & Formatting:** Use 2 spaces for indentation. Never use tabs.
- **Strict Mode:** The script must begin with `set -euo pipefail`.
- **Global Settings:** Configuration is stored in the associative array `TARGET_STATE` and the deterministic index array `TARGETS` at the top of the script.
- **Output prefixes:**
  - `→` for directory processing targets
  - `+` for added links
  - `✕` for disabled/wiped tool roots
  - `↻` for replaced links
  - `⚠` for warnings
  - `−` for removed/pruned links

### Representative Script Pattern

```bash
# Checking if link points to expected target
if [[ -L "$link_path" ]]; then
  current="$(readlink "$link_path")"
  if [[ "$current" == "$expected" ]]; then
    [[ $VERBOSE -eq 1 ]] && echo "  = $skill_name (already linked)"
    skipped=$((skipped + 1))
    continue
  fi
fi
```

## Project-specific rules

- **Tool Root Wiping Gotcha:** Disabling a target (setting its `TARGET_STATE` value to `0`) wipes the parent directory (`dirname` of target path, e.g. `~/.windsurf/` instead of `~/.windsurf/skills/`). Always preserve this logic unless instructed otherwise.
- **Source Scans:** Only directories directly containing a `SKILL.md` file are considered valid source skills. Dirs without a `SKILL.md` must be ignored.
- **Safe Link Writing:** Never overwrite real directories or non-symlinked files.
- **Targets:** 16 tool dirs total — 9 enabled by default, 7 disabled. Full list + paths live in the `TARGET_STATE` block; mirrored in README.md.

## Where to look

- Product Scope: `docs/PRODUCT.md`
- Architecture & Flow: `docs/ARCHITECTURE.md`
- Settled decisions: `docs/DECISIONS.md`
- Security rules: `docs/SECURITY.md`
- Test strategy: `docs/TESTING.md`
