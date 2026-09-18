# ARCHITECTURE.md

> Technical structure. How the pieces fit together. Not how to use the project (see AGENTS.md).

## High-level diagram

```
                           ┌──────────────────────────────┐
                           │   ~/.agents/skills/          │
                           │     (Source of Truth)        │
                           └──────────────┬───────────────┘
                                          │
                                          │ scans subdirs
                                          ▼
                               ┌──────────────────┐
                     ┌─────────┤   sync-skills    ├─────────┐
                     │         │     (Script)     │         │
                     │         └──────────┬───────┘         │
                     │                    │                 │
    wipes disabled   │                    │ links enabled   │ prunes broken
    targets          ▼                    ▼                 ▼
             ┌───────────────┐    ┌───────────────┐  ┌──────────────┐
             │ ~/.windsurf/  │    │ ~/.claude/    │  │ ~/.cursor/   │
             │ (Wiped root)  │    │   skills/     │  │   skills/    │
             └───────────────┘    └───────────────┘  └──────────────┘
```

## Layers

Since `sync-skills` is a single Bash script, it does not use a traditional web stack. Its structure consists of:

### 1. Configuration Block (Static Definition)
- Pinned at the top of the script.
- Configures tool directories via two primary structures:
  - `TARGET_STATE`: Associative array matching target paths to `1` (enabled) or `0` (disabled).
  - `TARGETS`: Ordered array containing target paths to ensure deterministic, sorted terminal outputs during run/reporting.

### 2. Argument Parsing & Runtime State
- Standard `while` loop parsing options: `--dry-run`, `--force`, `--prune`, `--report`, `--verbose`, `--help`.
- Populates state variables: `DRY_RUN`, `FORCE`, `PRUNE`, `VERBOSE`, `REPORT`.

### 3. Source Discovery Layer
- Scans `SOURCE` (default: `~/.agents/skills`) using `find -mindepth 1 -maxdepth 1 -type d -print0` to safely handle directory names with spaces.
- Checks each directory for the signature file `SKILL.md`. Only directories containing `SKILL.md` are added to the list of active skills.

### 4. Operations Engine
Depending on flags, performs:
- **Report Audit:** Loops over targets, checks symlink statuses (using `readlink` and `[[ -e ]]` / `[[ -L ]]` tests) and outputs a formatted table.
- **Wiping Phase:** Loops over targets, detects disabled ones, and runs `rm -rf` on their parent tool directory (e.g. `~/.windsurf/`).
- **Linking Phase:** Loops over enabled targets, creates them via `mkdir -p`, and writes individual symlinks (`ln -s`).
- **Pruning Phase:** If `-p/--prune` is passed, runs `find` on enabled targets to identify and remove broken links.

## Execution Flow

```
1. Script start (runs under bash -euo pipefail)
2. Parse CLI Options -> Set Runtime Flags
3. Scan Source Folder -> Populate SKILLS array (filter on SKILL.md presence)
4. IF Report Mode:
     Perform read-only link audits -> Print table -> Exit 0
5. IF Normal Mode:
     A. Loop over TARGETS:
        If disabled -> WIPE tool root dir (rm -rf dirname)
     B. Loop over TARGETS:
        If enabled:
          - Ensure target directory exists (mkdir -p)
          - Loop over SKILLS:
              Verify existing links.
              - If correct -> Skip
              - If stale & --force -> Remove link & link to new target
              - If real dir -> Skip & warn
              - Else -> Link
          - IF --prune -> Scan target and remove broken symlinks (rm link)
6. Output final operation counts -> Exit 0
```

### Error Paths

- **Source folder missing:** Immediate abort with exit code `1`.
- **Linking failure:** Logs failure to stdout and increments error count (does not crash target processing loop).
- **Bash compatibility error:** Handled by shell if running under standard Bash; `set -euo pipefail` ensures script aborts on unexpected failures.

## Cross-cutting concerns

- **Target Dir Deletion:** Setting a target state to `0` triggers `rm -rf $(dirname "$target")` to clean up the IDE's active profiles. This removes the entire tool directory, not just the `skills` subdirectory.
- **Dry-run Execution:** The `run()` wrapper intercepts commands like `mkdir`, `ln`, and `rm` and prints them instead of executing them.
- **Nullglob / Space Safety:** All path operations are quoted, and scan outputs are read via null-delimited find pipelines (`find -print0 | read -d ''`).

## Patterns we use

- **Shell Strictness:** `set -euo pipefail` ensures robust failure handling.
- **Associative Arrays:** `declare -A TARGET_STATE` provides O(1) status lookups.
- **Safe Command Wrappers:** Using `run` wrapper for all state-changing commands allows unified dry-run support.
- **Pure Shell/Coreutils:** No dependency on python, ruby, or modern utilities ensures the script runs out-of-the-box on clean Unix servers/workstations.

## Patterns we avoid

- **Interactive Prompts:** Avoid asking the user for confirmation on wipes or link replacements. Keep choices driven by declarative script configuration or CLI flags.
- **Implicit Overwrites:** Never replace user's custom directories or custom scripts at target paths. A real directory at a target skill path is skipped to avoid data loss.
- **Fuzzy Skill Matches:** Avoid linking folders that do not have `SKILL.md`.

## Installation

The script is deployed manually by copying it into a directory in the user's `PATH` (typically `~/` or `/usr/local/bin`) and making it executable:
```bash
cp sync-skills ~/sync-skills && chmod +x ~/sync-skills
```
No package building or compilation step is required.
