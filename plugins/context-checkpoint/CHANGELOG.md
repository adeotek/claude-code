# Changelog

All notable changes to `context-checkpoint` will be documented here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2026-04-22

### Added
- `update-context` skill: merges an existing checkpoint with a new session summary — replaces Current State and Next Steps, appends to Decisions Made and Notes, preserves Goal unless the session has pivoted

### Changed
- `save-context` now recognised when invoked with just a checkpoint name (no "save as" phrasing required)
- `load-context` explicitly shows an available-checkpoints menu on bare invocation (no arguments)

## [1.0.0] - 2026-04-22

### Added
- `save-context` skill: saves a named session checkpoint to `.memory/<name>.md` with fixed sections (Goal, Current State, Key Files, Decisions Made, Next Steps, Notes)
- `load-context` skill: loads a saved checkpoint and re-establishes context conversationally
- Git state capture (branch + last 3 commits) when inside a git repo
- Overwrite protection: prompts before replacing an existing checkpoint
- Auto-listing of available checkpoints when no name is provided to `load-context`
