# DECISIONS.md

> Settled architectural decisions. **Append-only.** Never edit or delete past entries (one exception: `Status` field of a superseded entry).
> If a decision is reversed, append a new entry with `Supersedes: #NNNN`.

## How to use this file

- Read before contradicting any documented pattern.
- New decisions are added with the next sequential number.
- Each entry has: number, title, date, status, context, decision, consequences, optional `Supersedes`.

## Status values

- `Proposed` — under discussion
- `Accepted` — current
- `Superseded by #NNNN` — replaced by a later decision (only the Status field can be edited on a superseded entry)
- `Deprecated` — no longer applies but no replacement

---

## 0001 — Project initialized

**Date:** 2026-06-12
**Status:** Accepted
**Context:** Project scaffolded with the project-ninja skill to align code and documentation.
**Decision:** Establish AGENTS.md and docs/ as the source of truth for AI agent context. Cross-references are owned per `references/cross-references.md` in the project-ninja skill. Simplify CLAUDE.md and GEMINI.md to one-line pointers to AGENTS.md.
**Consequences:** All AI tools (Claude Code, Antigravity, Codex, Cursor, etc.) use AGENTS.md. Decisions affecting the codebase must be appended here.
