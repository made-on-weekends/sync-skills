# TESTING.md

> Testing strategy and verification procedures.

Since `sync-skills` is a zero-dependency local Bash utility, there is no automated test harness (like bats or shellspec) currently configured. All validation is performed via syntax check and manual verification.

> ⚠️ **Wipe warning:** A real `./sync-skills` run executes the wiping phase —
> it `rm -rf`s the parent dir of every *disabled* target (`~/.windsurf`,
> `~/.continue`, `~/.roo`, `~/.trae`, `~/.kiro`, `~/.qoder`, `~/.codebuddy`).
> For local testing prefer `--dry-run`, or temporarily set all `TARGET_STATE`
> values to `1` before running against your real `$HOME`.

## Linting & Syntax Validation

Before staging or proposing script changes, verify the syntax using the Bash built-in check:
```bash
bash -n sync-skills
```
This checks for syntax errors (like mismatched quotes, unclosed blocks, or missing braces) without executing the code.

## Verification Scenarios

When modifying synchronization behavior, verify the following test cases manually:

### 1. Dry Run Verification
Verify that running the script with the dry-run flag prints correct output and does not write any links or files to disk.
```bash
# Set a custom test source and target
SKILLS_SOURCE=./test-source ./sync-skills --dry-run
```
Expected output:
- List of dry-run actions printed (e.g. `[dry-run] mkdir -p`, `[dry-run] ln -s`).
- No files actually created in target directories.

### 2. Standard Sync Verification
Verify that active skills are linked to enabled targets.
- Pre-requisite: A source folder exists containing at least one subfolder with a `SKILL.md` file.
- Action: Run `./sync-skills` (or with custom `SKILLS_SOURCE`).
- Expected:
  - Symlinks created in all enabled target directories (e.g. `ls -l ~/.claude/skills/`).
  - Target files are actual symlinks pointing to the correct source skill folders.

### 3. Reporting/Audit Verification
Verify that report mode prints the accurate state of all targets.
```bash
./sync-skills --report
```
Expected:
- Formatted table outputting correct status labels.
- Counts of `CORRECT`, `STALE`, `BROKEN`, and `REAL-DIRS` correspond to the actual filesystem state.

### 4. Pruning Verification
Verify that broken links are removed.
- Pre-requisite: Create a broken symlink in one of the target folders pointing to a non-existent skill.
- Action: Run `./sync-skills --prune`.
- Expected: The broken symlink is removed from the target folder, and deleted links count is incremented in summary output.

### 5. Force Replacement Verification
Verify that stale symlinks are replaced when force is specified, and warning is printed when force is omitted.
- Pre-requisite: Point a target symlink to a different directory.
- Action:
  1. Run `./sync-skills` (without `-f`) and verify it warns about the existing symlink and skips.
  2. Run `./sync-skills --force` and verify it replaces the link.
