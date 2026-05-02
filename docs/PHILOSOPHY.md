# Coding philosophy & design choices

The agents are opinionated. Here's where they stand on the famous architectural debates so you can decide if it matches yours, and the design rationale for the choices baked into the pipeline.

## TL;DR

| Principle | Stance |
| :--- | :--- |
| **Pragmatic Laravel, not DDD** | Eloquent *is* the repository. No bounded contexts, no aggregate roots. |
| **Actions over Services** | One Action per use case (`CreateOrder`), not `OrderService::create()`. |
| **SOLID, selectively** | SRP enforced hard. The other four only when they earn their weight. |
| **KISS + YAGNI** | If deleting the cleverest line keeps the test green, delete it. |
| **TDD** | Failing test first, then minimal impl. Always. |
| **Convention over configuration** | Laravel's idiomatic structure — no `app/Services/` etc. unless you already have them. |
| **Comments justify *why***, not *what* | A comment that restates the code is a code smell. Rename or split. |

---

## Pragmatic Laravel, not academic DDD

The agents do not layer DDD on top of Laravel. No bounded contexts, no aggregate roots, no Repository pattern over Eloquent — Eloquent **is** the repository[^ddd-laravel]. We trust the framework's defaults. If you need Hexagonal Architecture, this isn't the right tool.

## Actions over Services

Write paths live in single-purpose **Action classes** (`app/Actions/{Domain}/{Verb}{Noun}.php`)[^actions]. One Action = one use case. A "service" with 20 methods is rejected on sight; it gets split per use case.

## SOLID — selectively, not religiously

| Principle | Enforced? | Why |
| :--- | :--- | :--- |
| **S** Single Responsibility | **Yes, hard rule** | Action does one thing; model owns persistence; controller routes |
| **O** Open/Closed | When extension is real | Premature `Strategy` patterns rejected |
| **L** Liskov Substitution | Where inheritance is used (rare) | Most code is composition |
| **I** Interface Segregation | When there's a second impl or test seam | One impl = no interface |
| **D** Dependency Inversion | Where boundaries cross (mailers, payment) | Don't inject `OrderRepository` if Eloquent works |

## KISS and YAGNI are non-negotiable

> *"If I delete the most clever line of this diff, does the test still pass?"* If yes, delete it.

This is in the `laravel-builder` system prompt verbatim. Premature flags, unused interfaces, configurability for hypothetical future needs — all rejected.

## Test-Driven Development

The builder writes a failing Pest test first, confirms red, writes the minimal code to make it green, then refactors[^tdd]. Not religion — LLMs without a passing test as a target hallucinate happy-path-only code.

## Convention over Configuration

The agents follow Laravel's idiomatic structure rather than introducing custom layers. They will not create `app/Services/`, `app/Repositories/`, or `app/DTOs/` unless your project already has them[^conventions].

## What this means in practice

- `app/Models/` stays small. Models own relations, casts, scopes, accessors. Nothing else.
- `app/Http/Controllers/` methods stay under 10 lines. Form Request → Action → Resource.
- `app/Actions/` grows feature by feature. One file per use case.
- Tests live next to the routes that exist. Feature tests cover happy + at least one failure path. Arch tests guard the rules you care about[^arch-tests].

If you want a different style — God services, fat models, Repository wrappers — these agents will fight you on it. Fork and adapt the system prompts.

---

## Design choices

### Why subagents and not skills

Both are markdown files with frontmatter — but they behave fundamentally differently[^subagents-skills]. Three things skills would break:

1. **Reviewer loses independence.** A skill-based reviewer would inherit the builder's full transcript and have anchoring bias — it has *already seen* the implementation by the time review starts. A subagent reviewer sees only the diff. That's where independent findings come from.
2. **Refinement can't run in parallel.** Subagents support real parallel execution[^parallel-cite]. Skills run inline in the main conversation, serialized. Sequential refinement means each downstream lens biases toward the upstream one — losing the cross-check.
3. **Main context collapses.** Architect deliberation, builder retries, reviewer scans — as subagents, all of that lives in *their* contexts. As skills, it lands in yours. By phase 3 you'd be out of room.

Skills are right when the rule is procedural and applies always (e.g. *"always run `pint --dirty` after edits"*) — those live inside agent system prompts. Subagents are for autonomous units of work that produce a deliverable.

### Why three lenses in refinement, not one big architect

1. **Cross-check value.** When db-architect plans an index strategy, ui-ux is independently planning the screen that uses it. The phase-planner's first job is to verify the three lenses agree. A single agent has no peer to disagree with.
2. **Parallel speed.** All three run simultaneously. Refinement that would take 15 minutes sequentially takes 5 in parallel.
3. **Specialization.** A single architect is a generalist that does everything okay. db-architect knows partial indexes, partitioning thresholds, generated columns. ui-ux knows empty/loading/error state coverage. Neither is a footnote in a generalist prompt — they're the whole prompt.

### Why an independent reviewer instead of having the builder review itself

Builders that review themselves rubber-stamp. They have sunk-cost feelings about the code they just wrote. The reviewer is a separate context that has only seen the diff — no attachment, no anchoring. The categories it flags (N+1, fat controller, Filament version mismatch) are exactly what a self-review misses.

---

## What these agents will NOT do

- Invent business logic without a spec — the architect asks clarifying questions until it's unambiguous
- Skip tests
- Modify `.env`, `composer.lock`, or run `composer update` without explicit instruction
- Commit on your behalf

---

[^ddd-laravel]: [Laravel docs — Eloquent](https://laravel.com/docs/12.x/eloquent) — Eloquent is an "ActiveRecord" implementation. Wrapping it in a Repository pattern adds indirection without removing the framework dependency.
[^actions]: [Spatie — Laravel Beyond CRUD: Actions](https://stitcher.io/blog/laravel-beyond-crud-03-actions) — popularized the Action class pattern in the Laravel community.
[^tdd]: [Pest docs — Writing Tests](https://pestphp.com/docs/writing-tests).
[^conventions]: [Laravel docs — Directory Structure](https://laravel.com/docs/12.x/structure).
[^arch-tests]: [Pest 3 — Arch testing](https://pestphp.com/docs/arch-testing).
[^subagents-skills]: [Claude Code — Subagents](https://code.claude.com/docs/en/agents.md) and [Skills](https://code.claude.com/docs/en/skills.md).
[^parallel-cite]: [Claude Code — Subagents § Coordination](https://code.claude.com/docs/en/agents.md) — main conversation can dispatch multiple subagents in parallel via multiple `Task` calls in one turn.
