---
name: update-context
description: This skill should be used when the user asks to "update context X", "update checkpoint X", "refresh context X", "update saved context X", "sync checkpoint X", "update session X", or "update my saved session X". Also use when the user invokes /context-checkpoint:update-context with or without a name argument, to merge new session progress into an existing checkpoint file.
---

# Update Context

Refresh an existing checkpoint in `.memory/<name>.md` by merging preserved content with a new summary from the current session. Current State and Next Steps are always replaced; Goal, Decisions Made, and Notes are preserved and extended.

## Steps

1. **Extract the filename** from the user's prompt. Use the same kebab-case normalization as `save-context` (e.g., "auth work" → `auth-work`).

2. **If no name was given**, list available contexts and ask the user which to update:
   ```sh
   ls .memory/*.md 2>/dev/null
   ```
   Present the names without the `.md` extension and without the `.memory/` path prefix.

3. **If the file doesn't exist** (`.memory/<name>.md` not found), tell the user and suggest creating it with `save-context` first.

4. **Read** `.memory/<name>.md` using the Read tool.

5. **Gather fresh git state** by running:
   ```sh
   git branch --show-current 2>/dev/null
   git log --oneline -3 2>/dev/null
   ```
   If either fails (not a git repo), omit the git line.

6. **Generate the merged update** using the section-by-section rules below.

7. **Write** the updated content back to `.memory/<name>.md`. No overwrite prompt is needed — updating is the explicit intent.

8. **Confirm** with a brief summary of what changed, e.g., "Updated `.memory/auth-work.md` — Current State and Next Steps refreshed, 2 new decisions added."

## Merge Rules

**Saved date and git metadata**: Always replace with today's date and fresh git state.

**Goal**: Preserve the original paragraph. If the current session has clearly pivoted to a different objective, update the paragraph and append a parenthetical note, e.g., *(updated YYYY-MM-DD: now focused on Y instead of X)*.

**Current State**: Always replace entirely — old state is stale by definition.

**Key Files**: Merge old and new. Keep entries still relevant to the work; add files created or meaningfully modified in this session; remove entries for files no longer relevant.

**Decisions Made**: Keep all existing decisions (historical context matters). Append any new non-obvious decisions from this session.

**Next Steps**: Re-derive from the current session. Mark items completed in this session with `- [x]`, remove obsolete items, add new ones, and re-order by priority.

**Notes**: Keep notes that are still applicable. Add new caveats, discoveries, or constraints from this session. Remove notes that are no longer relevant.

## Content Guidelines

Apply the same standards as `save-context`: be specific and concrete, avoid vague phrases, omit sections that have nothing meaningful to say. The goal is a compact, accurate aid to resumption — not a changelog.
