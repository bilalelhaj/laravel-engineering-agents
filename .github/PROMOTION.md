# Promotion drafts

Maintainer notes — copy/paste-ready text for posting this repo across channels. Not part of the public-facing repo content; lives here so it's versioned and editable.

---

## Twitter / X

### Launch tweet (option A — pragmatic)

> I built a Claude Code pipeline for Laravel.
>
> Three specialists refine your spec in parallel (architecture, schema, UI/UX). A planner slices the work into phases. A builder ships them test-first. An independent reviewer audits each phase.
>
> Detects Laravel 10–13, Pest 3/4, Filament 3/4/5.
>
> 🔗 https://github.com/bilalelhaj/laravel-engineering-agents

### Launch tweet (option B — story)

> v1: 3 agents, lucky agreement, no conflicts caught.
> v2: 6 agents, parallel refinement, found 5 cross-lens conflicts but the reviewer caught a defense-in-depth drift post-hoc (H11).
> v3: H11 became a planner-level rule. Plus an orchestrator. Plus a Filament family.
>
> Open source. MIT. Run on your own Laravel project.
>
> 🔗 https://github.com/bilalelhaj/laravel-engineering-agents

### Reply chain (after the launch tweet)

> 1/ The pipeline assumes nothing about your stack — agents read composer.json and adapt. Filament 3 vs 4 vs 5? Pest 3 vs 4? It detects.
>
> 2/ Real-run on a Laravel 13 / Pest 4 diary app: 13 new passing tests, 6 cross-lens conflicts auto-resolved, 1 substantive safety finding. Documented end-to-end → examples/real-run.md
>
> 3/ Why subagents and not skills? Independent context per phase. The reviewer doesn't see the builder's transcript — that's how it finds real issues. The README walks through three things skills would break.
>
> 4/ Built with the Anthropic plugin format → /plugin install laravel-engineering-agents@bilalelhaj/laravel-engineering-agents

### Tags to consider

`@laravelphp` `@filamentphp` `@AnthropicAI` `@pestphp`

---

## LinkedIn

> Just open-sourced **Laravel Engineering Agents** — a 12-subagent Claude Code pipeline for Laravel teams.
>
> The shape:
> - Three specialists refine your feature spec in parallel — application logic (`@laravel-architect`), data layer (`@laravel-db-architect`), UI/UX (`@laravel-ui-ux`)
> - A `@laravel-phase-planner` synthesizes their outputs into small, deliverable phases
> - `@laravel-builder` implements each phase test-first (Pest 3/4)
> - `@laravel-reviewer` audits independently — sees only the diff, no anchoring bias
> - Optional `@laravel-orchestrator` drives the whole loop with one prompt
>
> Plus a Filament family (`@filament-architect`, `@filament-builder`, `@filament-reviewer`) for admin-panel-heavy projects.
>
> The agents detect your stack from `composer.json` and adapt — Laravel 10/11/12/13, Pest 3/4, Filament 3/4/5, Livewire 3/4, Tailwind 3/4.
>
> Three things I'm proud of:
>
> 1. The **real-run.md** in the repo documents an end-to-end run on a real Laravel 13 project — 13 new passing tests, 6 cross-lens conflicts auto-resolved, plus the v1 → v2 → v3 evolution showing how a defense-in-depth drift caught by the independent reviewer became a planner-level rule in the next iteration.
>
> 2. The reviewer is **uncompromising about specific failure modes** — Schema N+1, fat controllers, Filament version-mismatch, defense-in-depth scoping deviations. Every finding is file:line with a concrete fix.
>
> 3. **KISS and SRP are non-negotiable.** No premature interfaces, no Repository pattern over Eloquent, no `app/Services/` unless your project already has it. The agents will fight you on it.
>
> MIT license. PRs welcome.
>
> 🔗 https://github.com/bilalelhaj/laravel-engineering-agents

---

## Reddit r/laravel

### Title

`I built a 12-subagent Claude Code pipeline for Laravel — refine, plan, build, review`

### Body

```
Open-sourced today: a multi-agent Claude Code pipeline for Laravel projects.

The flow: you give it a feature spec, three specialists refine it in parallel
(architect / db-architect / ui-ux). A phase-planner synthesizes them into
small deliverable phases. A builder ships each phase test-first. A reviewer
audits independently — sees only the diff, so it finds things a self-review
won't.

Detects Laravel 10–13, Pest 3/4, Filament 3/4/5, Livewire 3/4, Tailwind 3/4
from composer.json and adapts.

The repo includes a real end-to-end run on a Laravel 13 / Pest 4 diary app —
13 new passing tests, 6 cross-lens conflicts auto-resolved, plus a
v1 → v2 → v3 evolution where a defense-in-depth drift caught by the reviewer
in v2 became a planner-level rule in v3.

Install via /plugin install or git clone + cp. MIT.

Repo: https://github.com/bilalelhaj/laravel-engineering-agents
Real-run example: https://github.com/bilalelhaj/laravel-engineering-agents/blob/main/examples/real-run.md

Happy to answer questions about the design choices — especially "why subagents
and not skills?" (3-pain-points answer in the README).
```

---

## Laravel News submit

Submit URL: https://laravel-news.com/links

```
Title:
Laravel Engineering Agents — a 12-subagent Claude Code pipeline

URL:
https://github.com/bilalelhaj/laravel-engineering-agents

Description:
A multi-agent Claude Code pipeline tailored for Laravel teams. Three
specialists refine your spec in parallel — laravel-architect (app
boundaries), laravel-db-architect (schema, indexes, scale), laravel-ui-ux
(screens, flows, accessibility). A phase-planner synthesizes their outputs
into small phases, then laravel-builder ships each phase test-first (Pest
3 / 4) while laravel-reviewer audits independently. An optional
laravel-orchestrator drives everything from one prompt.

Includes a Filament family (filament-architect, filament-builder,
filament-reviewer) and on-demand agents for debugging and major-version
migrations.

Adapts to your stack — Laravel 10–13, Pest 3/4, Filament 3/4/5, Livewire
3/4, Tailwind 3/4 — by reading composer.json. Hard rules on KISS, SRP, no
premature abstractions, no Repository pattern over Eloquent.

The repo includes a documented end-to-end real-run on a Laravel 13 / Pest 4
project — 13 tests passing, 6 cross-lens conflicts auto-resolved, plus the
v1 → v2 → v3 evolution showing how lessons fed back into the prompts.

Install via /plugin install or git clone + cp. MIT.

Author:
Bilal El Haj — https://github.com/bilalelhaj
```

---

## Anthropic plugin marketplace submission

Submit URL: https://claude.ai/settings/plugins/submit

Description (≤ 300 chars):

```
12-subagent Claude Code pipeline for Laravel: parallel spec refinement
(architect + db + ui), phase planning, test-first build, independent
review. Filament family included. Detects Laravel 10–13, Pest 3/4,
Filament 3/4/5. MIT.
```

Long description (paste the README's "What this gives you" section).

Categories: `developer-tools`, `php`, `laravel`, `multi-agent`, `code-review`.

Screenshots: capture the README mermaid diagram + the demo terminal block.

---

## asciinema recording (when you do it)

```bash
brew install asciinema agg

cd /Users/bilalel/Documents/coding/tagebuch
asciinema rec demo.cast

# In the recording:
claude
> @laravel-orchestrator implement docs/project-description.md
# (let the pipeline run)
# Ctrl+D to stop

# Convert to GIF
agg demo.cast demo.gif

# Or upload for embedded player
asciinema upload demo.cast

# Embed in README:
# [![asciicast](https://asciinema.org/a/<id>.svg)](https://asciinema.org/a/<id>)
```

Replace the static demo block in the README with the link once recorded.
