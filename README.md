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

## When you want more

The other 15 agents are documented separately so this README stays scannable:

| Want to know… | Read |
| :--- | :--- |
| All 18 agents, the pipeline, stack support | [`docs/AGENTS.md`](docs/AGENTS.md) |
| Coding philosophy & design choices (KISS / SRP / why subagents not skills) | [`docs/PHILOSOPHY.md`](docs/PHILOSOPHY.md) |
| Connecting Linear / ClickUp / Trello / GitHub Issues | [`docs/INTEGRATIONS.md`](docs/INTEGRATIONS.md) |
| A real end-to-end run (Laravel 13 + Pest 4) | [`examples/real-run.md`](examples/real-run.md) |
| TODO.md format the task runner expects | [`examples/TODO.example.md`](examples/TODO.example.md) |
| Contributing | [`CONTRIBUTING.md`](CONTRIBUTING.md) |

## Stack

Detects from `composer.json`: Laravel 10–13, PHP 8.2+, Pest 3/4, Filament 3/4/5, Livewire 3/4, Tailwind 3/4. No version pinning — agents adapt.

## License

[MIT](LICENSE) © Bilal El Haj
