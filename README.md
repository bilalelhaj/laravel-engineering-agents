# Laravel Engineering Agents

> Seventeen Claude Code subagents for Laravel — a build pipeline (refinement →
> planning → build → review), a Filament family for admin panels, and seven
> on-demand specialists for debugging, version migrations, DevOps, performance,
> security, business-priority triage, and queue-style task running over a
> `TODO.md` / Linear / ClickUp / GitHub Issues.

**The standard build pipeline:**

```mermaid
flowchart LR
    O["@laravel-orchestrator<br/><sub><i>optional one-prompt driver</i></sub>"]
    Spec[Spec]

    O -.->|dispatches all| Spec
    Spec --> A["@laravel-architect"]
    Spec --> D["@laravel-db-architect"]
    Spec --> UI["@laravel-ui-ux"]
    Spec -. if Filament .-> FA["@filament-architect"]
    A & D & UI & FA --> P["@laravel-phase-planner"]
    P --> B["@laravel-builder"]
    P -. if Filament .-> FB["@filament-builder"]
    B & FB --> R["@laravel-reviewer"]
    B & FB -. if Filament .-> FR["@filament-reviewer"]
    R & FR -->|findings| Spec
```

**Plus a backlog flow over the same pipeline:**

```mermaid
flowchart LR
    Backlog["TODO.md / Linear / ClickUp /<br/>Trello / GitHub Issues"]
    Backlog --> TR["@laravel-triage<br/><sub>ranks P0–P4</sub>"]
    TR -->|recommended order| Human((human approves))
    Human --> TK["@laravel-tasks<br/><sub>runs items one by one,<br/>routes by classification</sub>"]
    TK -.-> Pipe["the build pipeline above<br/>or a specialist below"]
```

**Five more specialists you invoke directly when you need them**, no pipeline involved:

| | |
| :--- | :--- |
| `@laravel-debugger` | A test goes red unexpectedly |
| `@laravel-migrator` | Major version upgrade (L10→11→12→13, Filament 3→4→5, Pest 3→4) |
| `@laravel-devops` | Dockerfile / Compose / CI/CD / deployment / online migrations |
| `@laravel-perf` | Caching / queues / rate-limit / p95 / observability |
| `@laravel-security` | Auth / crypto / headers / CVE / GDPR / 2FA |

**Generic Laravel agents** — load on every project:

| Agent | Lens | Output |
| :--- | :--- | :--- |
| `laravel-orchestrator` | Optional driver — runs the whole pipeline end-to-end so you don't dispatch each phase yourself | progress log + final summary |
| `laravel-architect` | App boundaries, events, integrations, auth | `docs/refinement/architecture.md` |
| `laravel-db-architect` | Schema, indexes, scale, big data, queries | `docs/refinement/database.md` |
| `laravel-ui-ux` | Screens, flows, states, accessibility | `docs/refinement/ui-ux.md` |
| `laravel-phase-planner` | Synthesis → small deliverable phases | `docs/phases.md` |
| `laravel-builder` | Test-first impl, KISS + SRP | code + tests |
| `laravel-reviewer` | Independent per-phase audit | review report |

**Filament family** — load only if your project uses [Filament](https://filamentphp.com). Run alongside the generic agents:

| Agent | Lens | Output |
| :--- | :--- | :--- |
| `filament-architect` | Panel layout, resource organization, tenancy, plugins, custom pages, widgets | `docs/refinement/filament.md` |
| `filament-builder` | Builds Resources / Pages / Widgets / Clusters with `make:filament-*` generators, Schema-based forms, `Livewire::test()` patterns | code + tests |
| `filament-reviewer` | Independent Filament-specific audit — Schema N+1, plugin compat, tenancy leaks, v3↔v4↔v5 stale syntax | review report |

**On-demand agents** — not part of the standard pipeline; invoke them when you need them:

| Agent | When | Output |
| :--- | :--- | :--- |
| `laravel-debugger` | A test goes red unexpectedly, a stack trace points at vendor code, "works in dev but not in CI". Forms 3–5 hypotheses, narrows with cheap commands, hands you a minimal patch description. **Read-only** — does not implement the fix. | debug report |
| `laravel-migrator` | Major version upgrades — Laravel 10→11→12→13, Filament 3→4→5, Pest 3→4, Livewire 3→4, Tailwind 3→4. Runs official upgraders, applies mechanical syntax shifts, surfaces non-mechanical decisions. One major at a time, stops on red. | migration report + diff |
| `laravel-devops` | Dockerfile + Compose layout, CI/CD, deployment strategy, online migrations for live tables, image-size and cost trade-offs. Conservative — plans first, edits with explicit "go". | `docs/devops.md` |
| `laravel-perf` | Caching strategy, queue architecture (Horizon, retry/backoff, dead-letter), rate-limiting, p95-latency budgets, Sentry / Pulse instrumentation. Profile before optimizing. | `docs/perf.md` |
| `laravel-security` | Auth (password hashing, 2FA, sessions, tokens), authorization (Policies, tenancy), cryptography (encrypted casts, signed URLs), input validation, XSS / CSRF / SSRF, HTTP headers, dependency CVEs, secret management, GDPR. Threat-models every finding. | `docs/security.md` |
| `laravel-tasks` | Reads a `TODO.md` (or Linear / ClickUp / Trello / GitHub Issues via MCP), classifies each item, routes to the right pipeline (orchestrator for features, debugger for bugs, migrator for upgrades, security for audits, etc.), runs sequentially, flips checkboxes on success. One task at a time, stops on failure. | task-run report + updated `TODO.md` |
| `laravel-triage` | Reads the same backlog sources and ranks each item by business priority (P0 burning fire / P1 production bug / P2 customer-blocking / P3 standard / P4 backlog). Reads `docs/business-context.md` if present. Recommends order — never reorders the source. Use before sprint planning or when bug-fix vs feature tension exists. | triage report (ranked list with reasoning) |

## Install

**Option A — Plugin (recommended):** if your Claude Code session has the plugin marketplace enabled[^plugins], install with one command:

```
/plugin install laravel-engineering-agents@bilalelhaj/laravel-engineering-agents
```

**Option B — Manual copy:**

```bash
git clone https://github.com/bilalelhaj/laravel-engineering-agents.git
cp -r laravel-engineering-agents/.claude/agents/* .claude/agents/
```

Either way: restart Claude Code or run `/agents` — the seventeen agents appear in the list.

[^plugins]: [Claude Code — Plugins](https://code.claude.com/docs/en/plugins.md) — `/plugin install` reads the `.claude-plugin/plugin.json` manifest from the linked GitHub repo. The same agents work via manual `cp` if you don't use the plugin system.

## Demo

A run on a real Laravel 13 / Pest 4 project (the [`real-run.md`](examples/real-run.md) feature):

```text
$ claude

> @laravel-orchestrator implement docs/project-description.md

🟢 Detected: Laravel 13, PHP 8.3, Pest 4, Livewire 4 + Flux 2, SQLite

🟡 Phase B — refinement (3 subagents in parallel)
   ├─ @laravel-architect ........... ✓  docs/refinement/architecture.md
   ├─ @laravel-db-architect ........ ✓  docs/refinement/database.md
   └─ @laravel-ui-ux ............... ✓  docs/refinement/ui-ux.md
   wall-clock: 2m 56s   (sequential would be 8m+)

🟡 Phase C — phase planning
   └─ @laravel-phase-planner ....... ✓  docs/phases.md
      conflicts auto-resolved: 5
      ├─ tags.show vs tag.show          → tags.show
      ├─ {tag:id} vs {tag:slug}         → {tag:slug}
      ├─ normalized_name vs slug        → slug
      ├─ SyncTagsForEntry vs Attach…    → architect's names
      └─ sidebar 1-link vs 15-recency   → UI's design

🟡 Phase D — build / review (3 of 16 phases this run)
   Phase 1 — Schema + factory
      ├─ @laravel-builder ......... ✓  4 files, 1 test, 12 assertions
      ├─ pest …………………………………………… ✓  green
      ├─ pint …………………………………………… ✓  clean
      └─ @laravel-reviewer ........ ✓  0 critical · 0 high · 2 medium

   Phase 2 — Tag model + relations + forUser scope
      ├─ @laravel-builder ......... ✓  4 files, 5 tests, 13 assertions
      ├─ pest …………………………………………… ✓  green
      └─ @laravel-reviewer ........ ✓  0 critical · 0 high · 2 medium

   Phase 3 — Entry::scopeWithAllTags + scopeWithAnyTag
      ├─ @laravel-builder ......... ✓  2 files, 7 tests, 10 assertions
      ├─ pest …………………………………………… ✓  green
      └─ @laravel-reviewer ........ ⚠  0 critical · 1 high · 2 medium
         H12: defense-in-depth scoping deviation
              app/Models/Entry.php:54-60 — caller-discipline only

📋 Pipeline run summary
   Total subagent invocations …… 10
   New tests …………………………………… 13 (35 assertions)
   Suite ………………………………………… 27 pre-existing failures preserved
   Stopped at ……………………………… Phase 4 — H12 finding to triage with user
```

> Want a real recording? `asciinema rec demo.cast` while running the prompt above, then `agg demo.cast demo.gif`. PRs welcome.

## Use

**Easiest — let the orchestrator drive:**

```
@laravel-orchestrator implement docs/project-description.md
```

The orchestrator dispatches the three refinement agents in parallel, runs the planner, and loops `builder → pest → pint → reviewer` per phase. Stops on Critical / High findings and surfaces them.

**Manual control — drive each phase yourself:**

```
Read docs/project-description.md.

Run @laravel-architect, @laravel-db-architect, and @laravel-ui-ux IN PARALLEL
to refine it. Then @laravel-phase-planner to synthesize phases. Then build
phase by phase with @laravel-builder, running @laravel-reviewer after each.
```

**Queue-style — let the task runner work through your `TODO.md`:**

```
@laravel-tasks run through TODO.md
```

Drop a `TODO.md` at the repo root, write tasks as markdown checkboxes (optionally tagged `[feature]`, `[bug]`, `[migration]`, `[security]`, `[devops]`, `[perf]`). The agent classifies each item, dispatches to the right specialist, runs them one at a time, flips checkboxes on success. See [`examples/TODO.example.md`](examples/TODO.example.md) for the format.

**Triage first when the backlog is big:**

```
@laravel-triage rank my Linear sprint by priority
```

Returns a P0–P4 sorted list with one line of reasoning per item — bugs in production rank above features, security findings on production are always P0, customer-attached items beat internal ones. Reads `docs/business-context.md` if you keep one. Recommends order, never reorders the source. Run this before `@laravel-tasks` if the order matters.

**External task sources — Linear / ClickUp / Trello / GitHub Issues:**

Connect once via MCP, then point the task runner at the source instead of `TODO.md`:

```
@laravel-tasks run my Linear sprint
@laravel-tasks pick the bugs from my ClickUp list "Sprint 12"
```

Setup steps for each tool are in [`docs/INTEGRATIONS.md`](docs/INTEGRATIONS.md) — typically a one-time `claude mcp add ...` plus an OAuth login.

**When NOT to use the pipeline:** for a one-line bug fix or typo, just prompt Claude Code directly — the orchestra is overhead. The pipeline pays off when the change touches the database and needs tests.

**Example end-to-end run** — [`examples/real-run.md`](examples/real-run.md): real run on a Laravel 13 / Pest 4 diary app — 3 refinement agents in parallel, phase planning with 5 conflicts auto-resolved, 3 build/review cycles, 13 new tests passing, plus the v1 → v2 → v3 evolution.

## Coding philosophy — TL;DR

| Principle | Stance |
| :--- | :--- |
| **Pragmatic Laravel, not DDD** | Eloquent *is* the repository. No bounded contexts, no aggregate roots. |
| **Actions over Services** | One Action per use case (`CreateOrder`), not `OrderService::create()`. |
| **SOLID, selectively** | SRP enforced hard. The other four only when they earn their weight. |
| **KISS + YAGNI** | If deleting the cleverest line keeps the test green, delete it. |
| **TDD** | Failing test first, then minimal impl. Always. |
| **Convention over configuration** | Laravel's idiomatic structure — no `app/Services/` etc. unless you already have them. |
| **Comments justify *why***, not *what* | A comment that restates the code is a code smell. Rename or split. |

<details>
<summary>Full breakdown of the coding philosophy</summary>

### Pragmatic Laravel, not academic DDD
The agents do not layer DDD on top of Laravel. No bounded contexts, no aggregate roots, no Repository pattern over Eloquent — Eloquent **is** the repository[^ddd-laravel]. We trust the framework's defaults. If you need Hexagonal Architecture, this isn't the right tool.

### Actions over Services
Write paths live in single-purpose **Action classes** (`app/Actions/{Domain}/{Verb}{Noun}.php`)[^actions]. One Action = one use case. A "service" with 20 methods is rejected on sight; it gets split per use case.

### SOLID — selectively, not religiously

| Principle | Enforced? | Why |
| :--- | :--- | :--- |
| **S** Single Responsibility | **Yes, hard rule** | Action does one thing; model owns persistence; controller routes |
| **O** Open/Closed | When extension is real | Premature `Strategy` patterns rejected |
| **L** Liskov Substitution | Where inheritance is used (rare) | Most code is composition |
| **I** Interface Segregation | When there's a second impl or test seam | One impl = no interface |
| **D** Dependency Inversion | Where boundaries cross (mailers, payment) | Don't inject `OrderRepository` if Eloquent works |

### KISS and YAGNI are non-negotiable
> *"If I delete the most clever line of this diff, does the test still pass?"* If yes, delete it.

This is in the `laravel-builder` system prompt verbatim. Premature flags, unused interfaces, configurability for hypothetical future needs — all rejected.

### Test-Driven Development
The builder writes a failing Pest test first, confirms red, writes the minimal code to make it green, then refactors[^tdd]. Not religion — LLMs without a passing test as a target hallucinate happy-path-only code.

### Convention over Configuration
The agents follow Laravel's idiomatic structure rather than introducing custom layers. They will not create `app/Services/`, `app/Repositories/`, or `app/DTOs/` unless your project already has them[^conventions].

### What this means in practice
- `app/Models/` stays small. Models own relations, casts, scopes, accessors. Nothing else.
- `app/Http/Controllers/` methods stay under 10 lines. Form Request → Action → Resource.
- `app/Actions/` grows feature by feature. One file per use case.
- Tests live next to the routes that exist. Feature tests cover happy + at least one failure path. Arch tests guard the rules you care about[^arch-tests].

If you want a different style — God services, fat models, Repository wrappers — these agents will fight you on it. Fork and adapt the system prompts.

</details>

## Stack support

The agents detect your stack from `composer.json` and adapt — they're not pinned to one version.

| Component | Supported |
| :--- | :--- |
| Laravel | 10, 11, 12, 13[^laravel] |
| PHP | 8.2+ recommended (8.3+ ideal) |
| Pest | 3, 4[^pest] |
| Filament | 3, 4, 5[^filament] |
| Livewire | 3, 4 |
| Tailwind | 3, 4 |

**Filament 5 note:** v5 ships **no** Filament-API breaking changes vs. v4 — the major bump exists solely to require Livewire 4[^filament-v5]. The agents detect Filament v3 → v4 namespace shifts (different form/action namespaces) but treat v4 ↔ v5 as equivalent on the Filament side; they switch the Livewire 3 ↔ 4 conventions instead.

## Models

Subagents declare no `model:` field — they **inherit your Claude Code session model**[^model]. Run Opus → all agents Opus. Run Sonnet → all Sonnet.

For complex features, **Opus is recommended for the refinement phase**. Sonnet is fine for the build/review loop.

## Design choices

<details>
<summary><strong>Why subagents and not skills?</strong></summary>

Both are markdown files with frontmatter — but they behave fundamentally differently[^subagents-skills].

**Three things skills would break:**

1. **Reviewer loses independence.** A skill-based reviewer would inherit the builder's full transcript and have anchoring bias — it has *already seen* the implementation by the time review starts. A subagent reviewer sees only the diff. That's where independent findings come from.

2. **Refinement can't run in parallel.** Subagents support real parallel execution[^parallel-cite]. Skills run inline in the main conversation, serialized. Sequential refinement means each downstream lens biases toward the upstream one — losing the cross-check.

3. **Main context collapses.** Architect deliberation, builder retries, reviewer scans — as subagents, all of that lives in *their* contexts. As skills, it lands in yours. By phase 3 you'd be out of room.

**When skills are the right call:** procedural rules the agent always applies (e.g. *"always run `pint --dirty` after edits"*). Those live inside agent system prompts. Subagents are for autonomous units of work that produce a deliverable.

</details>

<details>
<summary><strong>Why three lenses in refinement, not one big architect?</strong></summary>

Three reasons:

1. **Cross-check value.** When db-architect plans an index strategy, ui-ux is independently planning the screen that uses it. The phase-planner's first job is to verify the three lenses agree. A single agent has no peer to disagree with.

2. **Parallel speed.** All three run simultaneously. Refinement that would take 15 minutes sequentially takes 5 in parallel.

3. **Specialization.** A single architect is a generalist that does everything okay. db-architect knows partial indexes, partitioning thresholds, generated columns. ui-ux knows empty/loading/error state coverage. Neither is a footnote in a generalist prompt — they're the whole prompt.

</details>

<details>
<summary><strong>Why an independent reviewer instead of having the builder review itself?</strong></summary>

Builders that review themselves rubber-stamp. They have sunk-cost feelings about the code they just wrote. The reviewer is a separate context that has only seen the diff — no attachment, no anchoring. The categories it flags (N+1, fat controller, Filament version mismatch) are exactly what a self-review misses.

</details>

## What these agents will *not* do

- Invent business logic without a spec — the architect asks clarifying questions until it's unambiguous
- Skip tests
- Modify `.env`, `composer.lock`, or run `composer update` without explicit instruction
- Commit on your behalf

## Roadmap

- [x] Six-agent refinement+build pipeline
- [x] `laravel-orchestrator` — drives the whole pipeline end-to-end
- [x] Plugin packaging (`.claude-plugin/plugin.json`)
- [x] Real-run lessons baked back: defense-in-depth conflicts caught at the planner layer
- [x] Filament family: `filament-architect`, `filament-builder`, `filament-reviewer`
- [x] On-demand: `laravel-debugger`, `laravel-migrator`, `laravel-devops`, `laravel-perf`, `laravel-security`
- [x] Task runner: `laravel-tasks` — reads `TODO.md` (or Linear / ClickUp / Trello via MCP) and dispatches each item
- [x] Backlog triage: `laravel-triage` — ranks items by business priority before the runner picks them up
- [ ] Submission to the [Anthropic plugin marketplace](https://claude.ai/settings/plugins/submit)

Issues and PRs welcome.

## License

[MIT](LICENSE) © Bilal El Haj

---

[^ddd-laravel]: [Laravel docs — Eloquent](https://laravel.com/docs/12.x/eloquent) — Eloquent is an "ActiveRecord" implementation. Wrapping it in a Repository pattern adds indirection without removing the framework dependency.
[^actions]: [Spatie — Laravel Beyond CRUD: Actions](https://stitcher.io/blog/laravel-beyond-crud-03-actions) — popularized the Action class pattern in the Laravel community.
[^tdd]: [Pest docs — Writing Tests](https://pestphp.com/docs/writing-tests).
[^conventions]: [Laravel docs — Directory Structure](https://laravel.com/docs/12.x/structure).
[^arch-tests]: [Pest 3 — Arch testing](https://pestphp.com/docs/arch-testing).
[^laravel]: [Laravel — Release notes](https://laravel.com/docs/12.x/releases).
[^pest]: [Pest docs](https://pestphp.com/docs/writing-tests). Pest 4 adds Browser testing.
[^filament]: [Filament 4 upgrade guide](https://filamentphp.com/docs/4.x/upgrade-guide), [Filament 5 upgrade guide](https://filamentphp.com/docs/5.x/upgrade-guide).
[^filament-v5]: [Filament v5 release notes — Laravel News](https://laravel-news.com/filament-5) and ["What actually changed in v5"](https://sadiqueali.medium.com/filament-v5-is-out-heres-what-actually-changed-and-what-you-should-upgrade-for-first-bbd27cbbd80f) — v5 = v4 + Livewire 4 requirement; no new Filament components or API changes.
[^model]: [Claude Code — Subagents § Configuration](https://code.claude.com/docs/en/agents.md) — when `model` is omitted, the subagent inherits the parent session's model.
[^subagents-skills]: [Claude Code — Subagents](https://code.claude.com/docs/en/agents.md) and [Skills](https://code.claude.com/docs/en/skills.md).
[^parallel-cite]: [Claude Code — Subagents § Coordination](https://code.claude.com/docs/en/agents.md) — main conversation can dispatch multiple subagents in parallel via multiple `Task` calls in one turn.
