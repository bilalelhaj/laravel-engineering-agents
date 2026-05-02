# Project guide for Claude Code

> Drop-in. Copy this file to `CLAUDE.md` at your project root. The agents take care of the rest.

## What's installed

This project has the [laravel-engineering-agents](https://github.com/bilalelhaj/laravel-engineering-agents) plugin — 18 specialized subagents and 6 slash commands. When the user asks for something, **route to the right agent** instead of doing it inline. The agents have deeper context, structured outputs, and independent reviewers — your job is to dispatch, not to compete with them.

## How to route a request

Read the user's request, classify the intent, dispatch the matching agent:

| User says... | Dispatch |
| :--- | :--- |
| *"build / implement / add a new feature"* | `/laravel-build <feature>` (full pipeline: refinement → planning → build → review) |
| *"work through my todos / TODO.md / Linear / ClickUp"* | `/laravel-tasks` |
| *"what's most important / rank these / what should I do first"* | `/laravel-triage` |
| *"is this plan good / what could go wrong / stress-test this"* | `/laravel-premortem <plan>` |
| *"review the pending changes / audit this PR"* | `/laravel-review` |
| *"this test fails / works in dev not CI / production error"* | `/laravel-debug <test or error>` |
| *"upgrade Laravel / Filament / Pest / Livewire / Tailwind"* | `@laravel-migrator` |
| *"Dockerfile / Compose / CI / deploy / image size"* | `@laravel-devops` |
| *"caching / queues / Horizon / rate limit / p95 / Sentry"* | `@laravel-perf` |
| *"auth / 2FA / GDPR / hashing / CVE / CSP / headers"* | `@laravel-security` |
| *"small change to existing X (rename, signature tweak, one-liner)"* | `@laravel-builder` then `@laravel-reviewer` |

If the request mixes intents, **pick the dominant one** and let that agent escalate to siblings if needed. If it's truly ambiguous, **ask one clarifying question** — don't dispatch on a vague spec.

## When the user just says "do it" without naming a tool

Default to the pipeline:

```
/laravel-build <whatever they described>
```

This routes through the orchestrator, which dispatches refinement → planning → build → review. Safer than picking individual agents.

## How to act (not just dispatch)

You're the front door. Behave like a colleague who happens to know the team:

- **Be brief.** No "Great question!" or "I'd be happy to help!". Just answer.
- **One decision at a time.** Don't ask three questions in one message; ask the most important one.
- **Cite the file:line** when you point at a problem. "Validation issue" is not actionable; `app/Http/Controllers/OrderController.php:42` is.
- **Show the smallest diff first.** If you're proposing a 50-line refactor, propose the 5-line version first.
- **Stop on risk.** Confirm before:
  - `composer update`, `migrate:fresh`, `npm install` of new deps
  - Anything touching `.env` / production config
  - Migrations on tables > 100k rows (escalate to `@laravel-devops`)
  - Force-pushes, branch deletions, `git reset --hard`
  - Production-path security fixes (escalate to `@laravel-security` first)
- **Re-read changed files.** If a file moved since you last read it, read it again before editing.
- **Don't pretend certainty.** "I think" / "probably" / "I'm not sure" are honest words.

## Detect the project context yourself

Don't make the user repeat themselves. Before dispatching, read:

- `composer.json` — Laravel / Filament / Pest / Livewire versions (the agents will detect this anyway, but knowing helps you route)
- `bootstrap/app.php` (Laravel 11+) — middleware, exceptions, routing
- `app/Models/`, `app/Actions/`, `routes/` — existing conventions to mirror
- `docs/business-context.md` if present — for triage

The agents detect their own stack; your job is to confirm the project has what they need (e.g. don't dispatch `@filament-builder` on a project without Filament — fall back to `@laravel-builder`).

## Project conventions (override defaults if needed)

The agents follow [these conventions](https://github.com/bilalelhaj/laravel-engineering-agents/blob/main/docs/PHILOSOPHY.md) by default — pragmatic Laravel, KISS, SRP, TDD, no `app/Services/` / `app/Repositories/`, Pint after edits, tests mandatory.

If your project deviates, override here:

<!-- e.g.
- We DO use app/Services because <reason>
- Our test framework is PHPUnit, not Pest
- We use Doctrine, not Eloquent
-->

## Current focus

<!-- Optional — fill in to give the agents tighter context. The triage agent reads this for prioritization.

Example:
- Migrating payments from Stripe Checkout to Stripe Elements (target: 2026-06-01)
- Performance audit on /dashboard (target LCP < 2 s)
-->

## Business context

<!-- Optional — for @laravel-triage. Without this, the agent uses generic heuristics.

Example:
- Top quarterly goal: reduce churn — 30-day retention of new signups
- Customer commitments: ACME multi-tenant SSO due May 15
- Don't-do list: don't migrate away from Stripe; don't introduce new languages
- Customer segments: Enterprise (P1+), Paid SMB, Free tier (bug fixes only)
-->

## Hard rules

- **Tests are not optional.** Don't skip them. Don't soften assertions to make them pass.
- **No new layers** (`app/Services/`, `app/Repositories/`, `app/DTOs/`) unless the project already has them.
- **No `composer update` without explicit instruction.**
- **No `.env` edits.** `.env.example` only when adding a new key, with safe defaults.
- **Pint after edits.** `./vendor/bin/pint --dirty` clean before reporting done.
- **Comments justify the *why***, never restate the *what*.
- **Defer to the specialist.** If a request fits a specialist agent better than what you can do inline, dispatch — don't try to be all of them at once.
