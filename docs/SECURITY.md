# SECURITY.md

> Security rules and threat model. Cross-references `ARCHITECTURE.md` for target directory wiping.

## Hard Rules

These rules are **absolute**. Violating any of them requires a `DECISIONS.md` entry that explicitly supersedes the rule, with security review.

1. **Never** dynamically evaluate or execute arbitrary script content downloaded from external links or sources.
2. **Never** perform recursive actions (like directory deletion or path scans) without absolute containment checks.
3. **Never** execute shell operations using unquoted variables that could result in shell splitting or glob expansions.
4. **Never** resolve paths using dynamic strings without checking that the target is contained within acceptable boundaries (e.g. preventing `/etc/` or `/` traversal).

## Threat Model & Containment

### 1. Accidental Home Folder Deletion (`rm -rf`)
- **Threat:** If the user configures a target state to `0` and defines target paths carelessly (e.g. `TARGET_STATE["${HOME}"]=0`), the script will resolve `dirname` to `/${HOME}/..` or `/home` and trigger a massive directory deletion.
- **Mitigation:**
  - Standard targets are hardcoded at the top of the script.
  - The script does not accept target directory overrides from environment variables (only `SKILLS_SOURCE` is overrideable).
  - Users are warned in documentation to verify target configuration before execution.

### 2. Path Traversal via custom SKILLS_SOURCE
- **Threat:** If `SKILLS_SOURCE` environment variable is overridden with a relative path (e.g. `../../..`), execution of `ln -s` may point links to locations outside the intended workspace.
- **Mitigation:**
  - `find` is configured to work exclusively on the absolute or relative path resolved by `$SOURCE`.
  - The script only creates links for folders containing `SKILL.md`, preventing arbitrary symlinking of files like `/etc/passwd`.

### 3. Argument Injection
- **Threat:** Malicious inputs inside CLI arguments.
- **Mitigation:**
  - The script uses standard getopt case loops that reject unknown flags and abort immediately on syntax errors.

## Incident Response

If a defect is identified that allows directory traversal or execution bypass:
1. **Identify** the specific line or utility call causing the leak.
2. **Quarantine** by warning users to avoid running the script.
3. **Fix** by patching the script.
4. **Record** the security fix in `docs/DECISIONS.md`.
