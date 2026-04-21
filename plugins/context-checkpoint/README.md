# context-checkpoint

A Claude Code plugin that saves named session checkpoints so you can pick up exactly where you left off across sessions.

## Overview

Work sessions often end mid-stream. `context-checkpoint` lets you snapshot the current conversation — goal, state, key files, decisions, and next steps — into a compact markdown file, then reload that context in any future session with a single phrase.

All checkpoint files are stored in a `.memory/` directory inside your current working directory, keeping them co-located with the project they describe.

## Installation

From the official Anthropic marketplace:

```
/plugin install context-checkpoint
```

Or directly from this repository:

```
/plugin marketplace add adeotek/claude-code
/plugin install context-checkpoint@adeotek-plugins
```

## Skills

After installation, two skills are available:

### `/context-checkpoint:save-context`

Summarizes the current session and saves it to `.memory/<name>.md`.

**Trigger phrases:**
- `"save this session as auth-work"`
- `"save context as feature-xyz"`
- `"checkpoint this work as payment-refactor"`
- `"remember this session as db-migration"`

**What gets saved:**
- **Goal** — what was being worked on and why
- **Current State** — what's done, in progress, or blocked
- **Key Files** — files created or meaningfully modified
- **Decisions Made** — non-obvious choices and their rationale
- **Next Steps** — prioritized action items
- **Notes** — caveats, gotchas, or context that's easy to forget
- **Git state** — current branch and last 3 commits (when inside a git repo)

If a checkpoint with the same name already exists, you'll be asked before it's overwritten.

### `/context-checkpoint:load-context`

Loads a previously saved checkpoint and re-establishes the context conversationally.

**Trigger phrases:**
- `"load context auth-work"`
- `"restore session feature-xyz"`
- `"resume work on payment-refactor"`
- `"continue where we left off on db-migration"`

If no name is provided, the available checkpoints in `.memory/` are listed. If the named file isn't found, the closest match is suggested.

## Checkpoint file format

Checkpoints are plain markdown files stored at `.memory/<name>.md`:

```markdown
# Context: auth-work
**Saved**: 2026-04-21 | **Directory**: /projects/app
**Branch**: feature/jwt-auth | **Recent commits**: a1b2c3 add middleware, d4e5f6 add tests

## Goal
Implement JWT authentication middleware for the API gateway.

## Current State
Core logic in `middleware.ts` is done and unit tests pass. PR not created yet.

## Key Files
- `src/auth/middleware.ts` — JWT validation logic
- `src/auth/middleware.test.ts` — unit tests (all passing)

## Decisions Made
- **HS256 over RS256**: simpler key management for a single-service deployment

## Next Steps
- [ ] Create PR
- [ ] Add integration test against real token issuer

## Notes
Token expiry is intentionally short (15 min) per security review feedback. Don't extend it.
```

## License

MIT
