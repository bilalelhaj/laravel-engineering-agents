---
description: Run the task runner over a backlog source (TODO.md, Linear, ClickUp, GitHub Issues).
argument-hint: <optional source — e.g. "TODO.md" or "my Linear sprint" or "ClickUp list 'Sprint 12'">
---

Dispatch `@laravel-tasks` to work through:

$ARGUMENTS

Default to `TODO.md` at the repo root if nothing is specified. One task at a time, sequential, stops on first failure. For external sources (Linear / ClickUp / Trello / GitHub Issues), the relevant MCP server must be configured — see `docs/INTEGRATIONS.md`.
