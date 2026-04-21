---
name: load-context
description: This skill should be used when the user asks to "load context", "restore session", "resume work on X", "load saved context", "recall X", "bring back X", "continue where we left off on X", "load X", "restore X", or "open context X". Use when the user references a previously saved work state by name.
---

# Load Context

Load a previously saved context from `.memory/<name>.md` in the current working directory and actively re-establish it in the conversation.

## Steps

1. **Extract the name** from the user's prompt using the same kebab-case normalization as save-context (e.g., "auth work" → `auth-work`).

2. **If no name was given**, list available contexts and ask the user which to load:
   ```sh
   ls .memory/*.md 2>/dev/null
   ```
   Present the names without the `.md` extension and without the `.memory/` path prefix.

3. **If the file doesn't exist** (`.memory/<name>.md` not found), list available contexts and suggest the closest match by name similarity. Do not fail silently.

4. **Read** `.memory/<name>.md` using the Read tool.

5. **Present the loaded context** in two parts:
   - Display the full file content so the user can scan it
   - Follow with a brief conversational restatement of the current state and next steps, so the context is immediately active without requiring the user to re-read the file

## Presentation Pattern

After displaying the file, restate the key points in 2–3 sentences:

> Loaded **auth-work** (saved 2026-04-21). We were implementing JWT authentication middleware — the core logic in `middleware.ts` is done and tests pass. The remaining work is creating the PR and adding an integration test.

Tailor this summary to what's most immediately actionable — prioritize **Current State** and **Next Steps** from the file. If the file has a **Notes** section with blockers or caveats, mention those too.

This positions the conversation to continue naturally. The user should be able to say "ok, let's do the next step" without needing to explain anything further.
