# Laravel Engineering Agents

> Multi-agent Claude Code workflow for Laravel — build features end-to-end,
> run a TODO list, debug failing tests, plus specialists for migrations,
> DevOps, performance, security, and pre-implementation stress-tests.

## Day 1 — three agents are enough

| | When you'd use it |
| :--- | :--- |
| **`@laravel-orchestrator`** | *"Build me this feature."* — runs the full pipeline (refinement → planning → build → review) end-to-end |
| **`@laravel-tasks`** | *"Work through my TODO.md."* — reads a list, classifies each item, dispatches to the right specialist, flips checkboxes |
| **`@laravel-debugger`** | *"This test was green yesterday — what broke?"* — read-only diagnosis, hands you a minimal patch description |

```bash
git clone https://github.com/bilalelhaj/laravel-engineering-agents.git
cp -r laravel-engineering-agents/.claude/agents/* .claude/agents/
```

Restart Claude Code (or run `/agents`), then try `@laravel-orchestrator implement <something small>`.

Plugin-marketplace alternative: `/plugin install laravel-engineering-agents@bilalelhaj/laravel-engineering-agents`.

## Slash commands (speed-dial over the agents)

The plugin also installs six slash commands so you don't have to type `@laravel-name` every time:

| | |
| :--- | :--- |
| `/laravel-build <feature>` | runs the full pipeline via `@laravel-orchestrator` |
| `/laravel-tasks [source]` | works through `TODO.md` or a named external source |
| `/laravel-triage [source]` | ranks the backlog by P0–P4 priority |
| `/laravel-premortem <plan>` | stress-tests a plan before implementation |
| `/laravel-review [scope]` | independent audit of pending changes |
| `/laravel-debug <test/error>` | hypothesis-driven diagnosis, hands you a patch |

## When you want more

| Want to know… | Read |
| :--- | :--- |
| All 18 agents, the pipeline, stack support | [`docs/AGENTS.md`](docs/AGENTS.md) |
| Coding philosophy & design choices (KISS / SRP / why subagents not skills) | [`docs/PHILOSOPHY.md`](docs/PHILOSOPHY.md) |
| Connecting Linear / ClickUp / Trello / GitHub Issues | [`docs/INTEGRATIONS.md`](docs/INTEGRATIONS.md) |
| A real end-to-end run (Laravel 13 + Pest 4) | [`examples/real-run.md`](examples/real-run.md) |
| `TODO.md` format the task runner expects | [`examples/TODO.example.md`](examples/TODO.example.md) |
| **Drop-in `CLAUDE.md`** for any new project — Claude reads it on session start, routes your requests to the right agent automatically | [`examples/CLAUDE.example.md`](examples/CLAUDE.example.md) |
| Optional hooks (Pint after edit, Pest on stop, stack on session start) | [`examples/hooks.example.json`](examples/hooks.example.json) |
| Contributing | [`CONTRIBUTING.md`](CONTRIBUTING.md) |

## Stack

Detects from `composer.json`: Laravel 10–13, PHP 8.2+, Pest 3/4, Filament 3/4/5, Livewire 3/4, Tailwind 3/4. No version pinning — agents adapt.

## License

[MIT](LICENSE) © Bilal El Haj
