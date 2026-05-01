# Project TODO

> Drop a copy of this file at your project root as `TODO.md`. Run `@laravel-tasks` and the agent works through the **Up next** section top-to-bottom. Items left in **Backlog** are ignored unless you tell the agent to run them.

## In progress

- [ ] Drop tasks here while you work on them — the agent skips this section.

## Up next

- [ ] [feature] Add tags for diary entries — many-to-many, scoped per user, max 20 per entry
- [ ] [bug] Fix N+1 on `/dashboard` — `EntryResource` accesses `$this->author->name` without `whenLoaded`
- [ ] [perf] Cache the public listing query — TTL up to 60 s, keys per page+filter
- [ ] [security] Audit login flow before public beta — 2FA enforcement, session rotation, throttling
- [ ] [devops] Cut Docker image below 200 MB — multi-stage, drop dev-deps from final layer
- [ ] [migration] Upgrade from Filament 4 to 5 — automated `vendor/bin/filament-v5` plus Livewire 3→4 cleanup

## Backlog

- [ ] [refactor] Move ad-hoc helpers in `app/Support/*` into named services
- [ ] [feature] Export entries to PDF
- [ ] [perf] Move queue from `database` driver to Redis + Horizon

## Done

- [x] Set up Pest 4 — 2026-04-12
- [x] Initial schema for entries — 2026-04-12

---

## Tag conventions (optional)

If you put a tag in brackets at the start of a task, the agent uses it for routing without classifying. Without a tag, the agent classifies from the text — usually fine, occasionally misses; tags are the safer way.

| Tag | Routes to |
| :--- | :--- |
| `[feature]` | `@laravel-orchestrator` (full pipeline) |
| `[bug]` | `@laravel-debugger` then `@laravel-builder` |
| `[migration]` | `@laravel-migrator` |
| `[security]` | `@laravel-security` |
| `[devops]` | `@laravel-devops` |
| `[perf]` | `@laravel-perf` |
| `[refactor]` | `@laravel-builder` then `@laravel-reviewer` |

## Behavior the agent follows

- One task at a time, top-to-bottom in **Up next**.
- Stops on the first failure and surfaces the reason.
- Asks before running anything risky (migrations, Dockerfile changes, multi-day scope).
- Flips `[ ]` to `[x]` only after suite + pint + reviewer all pass.
- Adds a one-line result and commit hash as a sub-bullet under each completed item.

## External sources

If you point the agent at Linear / ClickUp / Trello / GitHub Issues, it reads from there instead — but you must say so explicitly: *"@laravel-tasks run my Linear sprint"*. Otherwise it defaults to `TODO.md` at the repo root.
