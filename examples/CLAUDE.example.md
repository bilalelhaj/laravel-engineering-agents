# CLAUDE.md

> Drop a copy of this file at your project root as `CLAUDE.md`. Claude Code reads it at the start of every session — fill in your project's actual stack, conventions, and current focus. The agents read this too.

## Project

- **Stack**: Laravel <13 / 12 / 11>, PHP <8.3>, Pest <4>, Filament <5 / 4 / n/a>, Livewire <4 / n/a>, Tailwind <4 / 3>
- **Database**: <Postgres 16 / MySQL 8 / SQLite>
- **Hosting**: <Forge / Vapor / Render / self-hosted Docker / Vercel>
- **Cache / Queue**: <Redis + Horizon / database / sync>
- **Auth**: <Fortify / Sanctum / Passport / Breeze>
- **Repo layout**: <single-app / monorepo with /apps/web + /apps/api>

## Conventions

- All controllers go in `app/Http/Controllers/`, ≤ 10 lines per method
- Write paths in `app/Actions/{Domain}/{Verb}{Noun}.php`, single public `handle()` method
- API responses via `JsonResource` from `app/Http/Resources/`, never `return $model->toArray()`
- Validation in Form Requests, with `authorize()` filled
- Tests in `tests/Feature/` for happy + ≥1 failure path; arch tests in `tests/Arch.php`
- Commits follow conventional commits (`feat:`, `fix:`, `chore:`, `refactor:`, `test:`)
- No default exports of Eloquent models; no `app/Services/` / `app/Repositories/` folders
- Filament v4/5: form signature `public static function form(Schema $schema): Schema`

## Architecture decisions (the agents read these to stay consistent)

- <date>: Chose Pest over PHPUnit — datasets and architecture testing
- <date>: Chose Sanctum over Passport — SPA + mobile, no need for OAuth provider
- <date>: Chose Filament over Nova — open source, Schema-based forms in v4
- <date>: Chose Redis Horizon over `database` queue — observability + scaling

## Current focus

- <feature in flight, e.g. "Migrating payment system from Stripe Checkout to Stripe Elements">
- <performance / scaling target, e.g. "Performance audit on /dashboard, target LCP < 2 s">

## Rules for the agents

- Never edit > 5 files in one phase without showing me the plan first
- Run the existing suite before writing new code; surface unrelated failures
- If a task takes more than 5 phases, run `@laravel-premortem` first
- Never `composer update`, never modify `composer.lock` without explicit approval
- Never run `migrate:fresh` against a non-test DB
- Never modify `.env` — only `.env.example` for new keys, with safe defaults
- Always run `pint --dirty` after edits
- Tests are mandatory — never skip them, never soften assertions to make them pass

## Business context (optional but useful for `@laravel-triage`)

If you keep a separate `docs/business-context.md`, link it here. Otherwise inline:

- **Top goal this quarter**: <e.g. "Reduce churn — 30-day retention of new signups">
- **Customer commitments with deadlines**: <e.g. "ACME multi-tenant SSO — May 15">
- **Don't-do list**: <e.g. "Don't migrate away from Stripe">
- **Customer segments by importance**: <e.g. "1. Enterprise → P1+; 2. Paid SMB; 3. Free tier — bug fixes only">
