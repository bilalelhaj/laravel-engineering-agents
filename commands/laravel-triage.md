---
description: Rank a backlog by business priority (P0 burning fire → P4 backlog).
argument-hint: <optional source — e.g. "TODO.md" or "my Linear sprint">
---

Dispatch `@laravel-triage` to rank:

$ARGUMENTS

Default to `TODO.md` if nothing is specified. Reads `docs/business-context.md` if present (current top goal, customer commitments, don't-do list). Recommends order — never reorders the source. Run before `/laravel-tasks` if the order matters.
