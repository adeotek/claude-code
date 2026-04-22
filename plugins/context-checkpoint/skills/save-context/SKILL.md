---
name: save-context
description: This skill should be used when the user asks to "save this session", "save the current context", "save progress", "checkpoint this work", "save as X", "save context as X", "save context X", "remember this session as X", or mentions saving or capturing the current work state to a named file for later recall. Also use when the user invokes /context-checkpoint:save-context with just a checkpoint name as the argument (e.g., "/context-checkpoint:save-context auth-work").
---

# Save Context

Save a compact summary of the current session to `.memory/<name>.md` in the current working directory, using fixed sections for consistent, scannable output.

## Steps

1. **Extract the filename** from the user's prompt. Convert to kebab-case (e.g., "auth work" → `auth-work`, "JWT feature" → `jwt-feature`). The display name in the heading preserves the user's original phrasing.

2. **Check for an existing file.** If `.memory/<name>.md` already exists, ask the user whether to overwrite it before proceeding. Do not overwrite silently.

3. **Gather git state** by running these commands:
   ```sh
   git branch --show-current 2>/dev/null
   git log --oneline -3 2>/dev/null
   ```
   If either fails (not a git repo), omit the git line from the output entirely.

4. **Create the `.memory/` directory** in the current working directory if it doesn't exist:
   ```sh
   mkdir -p .memory
   ```

5. **Write `.memory/<name>.md`** using the template below, populated from the current conversation.

6. **Confirm** the saved file path to the user (e.g., "Saved to `.memory/auth-work.md`").

## Template

```markdown
# Context: <user's original name>
**Saved**: YYYY-MM-DD | **Directory**: <absolute cwd>
**Branch**: <branch> | **Recent commits**: <hash> <msg>, <hash> <msg>, <hash> <msg>

## Goal
<What was being worked on and why — one focused paragraph>

## Current State
<What's done, what's in progress, what's blocked — be specific about status>

## Key Files
- `<path/to/file>` — <what changed or why it matters>

## Decisions Made
- **<decision>**: <rationale — especially non-obvious choices>

## Next Steps
- [ ] <concrete action item>

## Notes
<Caveats, gotchas, blockers, or background that would be easy to forget>
```

Omit the git line entirely if not in a git repo. Omit any section that has nothing meaningful to say — never write "N/A" or leave a section empty.

## Content Guidelines

**Goal**: One paragraph describing the task and motivation. Focus on *why*, not just *what*.

**Current State**: Concrete status — "X is done, Y is in progress, Z is blocked because...". Avoid vague phrases like "mostly done".

**Key Files**: Only files that were created, meaningfully modified, or are central to understanding the work. Skip unchanged boilerplate or config files that weren't touched.

**Decisions Made**: Non-obvious choices where the reasoning matters for the future — e.g., "Used HS256 over RS256: simpler key management for a single service". Skip obvious or trivial choices.

**Next Steps**: Actionable items, ordered by priority. Be specific enough that they can be acted on without re-reading the full session.

**Notes**: Anything that would surprise a future reader or that isn't obvious from the file names — edge cases discovered, external dependencies, constraints from stakeholders, known issues left intentionally.

The goal is a compact aid to resumption, not a transcript. Err on the side of brevity in each section.
