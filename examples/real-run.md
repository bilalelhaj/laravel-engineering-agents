# Real Run — Tags feature on a Laravel 13 / Pest 4 diary app

> A complete end-to-end run of the six-agent pipeline on a real Laravel project.
> The diary app itself is private; this file captures the run faithfully so the
> behavior of the agents is observable without access to the project.

**Date:** 2026-05-01 · **Stack:** Laravel 13, PHP 8.3, Pest 4, Livewire 4 + Flux 2, SQLite · **Feature:** user-scoped tags on diary entries with filter / search / detail page

## TL;DR

- **6 subagents** run end-to-end across **3 of 16 planned phases** (Foundation slice)
- **13 new tests passing** · `pint` clean · all pre-existing tests preserved
- **5 cross-lens conflicts** auto-resolved by the phase planner
- **1 substantive safety finding** caught by the independent reviewer (on-plan, flagged for follow-up)

## What was built (Foundation slice — phases 1–3 of 16)

| Phase | Goal | Files | Tests | Reviewer findings |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Schema + factory: `tags` and `entry_tag` | 4 added | 1 (12 assertions) | 0 Critical, 0 High, 2 Medium |
| 2 | `Tag` model + relations + `forUser` scope + per-user route binding | 1 added, 3 modified | 5 (13 assertions) | 0 Critical, 0 High, 2 Medium |
| 3 | `Entry::scopeWithAllTags` + `scopeWithAnyTag` | 1 added, 1 modified | 7 (10 assertions) | 0 Critical, **1 High**, 2 Medium |

**Cumulative:** 13 new passing tests, 35 assertions.

## How the run unfolded

### Step 1 — Refinement, parallel (3 subagents simultaneously)

The user pointed all three at the same `docs/project-description.md`:

```
@laravel-architect, @laravel-db-architect, @laravel-ui-ux IN PARALLEL
to refine the spec.
```

Each wrote into `docs/refinement/`:

- `architecture.md` — 2 Actions (`AttachTagsToEntry`, `DeleteTag`), `TagPolicy`, sync-only flow, 5 explicit interactions with existing scopes (incl. lazy-create on the editor's debounce, the existing 2-char minimum on `/search`)
- `database.md` — `tags` table with `(user_id, slug)` UNIQUE + `(user_id, name)` index for sidebar sort; `entry_tag` pivot with composite PK + reverse index + `(tag_id, created_at)` recency index reserved for Phase 15
- `ui-ux.md` — chip row above the editor divider, `flux:autocomplete`, dedicated `/tags` page + recency sidebar, 6 explicit cap/limit surfaces, 5-method permissions surface, Tag-detail unauthorized = 404 (avoids leaking existence)

The agents reached **independent agreement** on:

- Hard delete + cascade pivot
- Case-insensitive matching via a `slug` column, display preserves first-typed casing
- Per-entry hard cap = 20
- Multi-select filter = AND
- Erlaubte Zeichen: letters, digits, `-`, `_`, space (comma reserved)

### Step 2 — Phase planning

`@laravel-phase-planner` synthesized the three refinement docs into 16 phases and **resolved 5 cross-doc conflicts**:

| # | Conflict | Source | Resolution |
| :--- | :--- | :--- | :--- |
| 1 | Route name `tags.show` vs `tag.show` | architect / ui | `tags.show` (REST plural) |
| 2 | Route binding `{tag:id}` vs `{tag:slug}` | architect / db | `{tag:slug}` (URL-safe, db has slug column) |
| 3 | Column name `normalized_name` vs `slug` | architect / db | unified `slug` for lookup + URL |
| 4 | Action names `SyncTagsForEntry` vs `AttachTagsToEntry` | ui / architect | architect's names |
| 5 | Sidebar: single link vs 15-item recency group | architect / ui | UI's design (architect deferred surface) |

Cross-cutting items (raised by ui-ux) were threaded through specific phases:

- structured error codes (`tag.user_cap_reached`, …) reserved for Phases 5/6/13
- `withCount` for delete-modal counter — Phases 9/11
- `(tag_id, created_at)` index for sidebar recency — reserved in Phase 1, consumed in Phase 15

### Step 3 — Build → review, sequential per phase

Three loops of `@laravel-builder` → `@laravel-reviewer` over phases 1–3.

#### Phase 3 highlights — what an independent reviewer adds

The reviewer flagged **H11** on Phase 3:

> **Cross-user safety relies on caller discipline.** `Entry::scopeWithAllTags` reads `entry_tag` directly without joining `tags` and constraining `tags.user_id`. The architect's contract puts user-scoping on the caller (`Entry::forUser($id)->withAllTags(...)`), so this is on-plan — but `database.md` Q3 explicitly recommended the defense-in-depth `tags.user_id` filter inside the subquery. The two refinements drifted apart here; the reviewer caught it.

The builder followed `architecture.md` faithfully. The phase-planner accepted both refinements without flagging this micro-conflict (it isn't a contradiction, just an asymmetry). It took the reviewer — reading only the diff — to surface it.

That's the value of independent review.

## v1 → v2 → v3 evolution

The agents shipped in three iterations. Each generation closed a gap the previous run surfaced.

### v1 → v2 — added rigour to the refinement docs

The first run produced consistent-but-shallow output. Seven mandatory sections were added to the refinement-agent prompts.

| | v1 | v2 |
| :--- | :--- | :--- |
| Cross-lens conflicts caught | 0 (lucky agreement) | 5 (planner) + 1 (reviewer) |
| Existing-code interactions documented | 0 | 5 in architect (lazy-create, `scopeSearch`, /search 2-char-min, sidebar, routes) |
| Permission UX behavior documented | No | All 5 `TagPolicy` methods, hide/disable/error rationale |
| Cap/limit UI surfaces documented | Vague | 6 explicit caps with input state + helper text |
| Self-check at end of doc | No | All three docs, all boxes ticked |
| Cross-impact tag on defaulted decisions | No | Mandatory `impacts: db, ui` etc. |
| Sibling-notes section | No | Mandatory; specifically addressed to db-architect / ui-ux |

### v2 → v3 — moved a real-world finding upstream

The build/review cycle on this run caught **H11**: cross-user safety relied on caller discipline. Architect & db-architect were both on-plan but disagreed silently — architect said "caller scopes", db-architect's plan recommended defense-in-depth, the planner accepted both without flagging. The reviewer caught it post-hoc.

v3 moves that detection to the planner so it's prevented, not flagged.

| | v2 | v3 |
| :--- | :--- | :--- |
| Defense-in-depth disagreements | Silently accepted, found by reviewer (H11) | Resolved by planner before phases are written |
| `architect` safety model | Implicit | Mandatory "Safety model for cross-tenant data" section: declare caller-scoped vs defense-in-depth |
| `db-architect` safety flag | Implicit | `[SAFETY-CRITICAL]` flag on stricter recommendations |
| `phase-planner` rule | "Stop on conflict" | "Defense-in-depth disagreements ARE conflicts; default to stricter" |
| Reviewer category | H11 (KISS/SRP only) | H12 added: "Defense-in-depth scoping deviation" |
| Pipeline orchestration | 7-line manual prompt | `@laravel-orchestrator implement docs/spec.md` (one-liner) |
| Install | `git clone && cp` | `/plugin install …@bilalelhaj/laravel-engineering-agents` |
| Repo CI | None | GitHub Actions: plugin manifest schema, agent frontmatter, README cross-references |

> **Note.** The v3 changes were applied to agent prompts and validated by the CI workflow, but **not** re-run end-to-end against `tagebuch` — the v2 run is the canonical "real run" captured here. A v3 re-run on a fresh feature would be the next dogfood cycle.

## Honest limitations of this run

1. **Subagent sandbox blocked `php`, `pest`, `pint`.** The builder agents wrote test files and migrations, but couldn't actually run the suite. The orchestrating session ran them on the builder's behalf and reported the result back. In a real Claude Code session with proper Bash permissions, the builder would run them inline.
2. **Only 3 of 16 phases ran.** Phases 4–16 cover Actions, Policy, pages, editor UI — same loop pattern, more total runtime.
3. **Reviewer is read-only.** H11 was flagged but not auto-fixed. In a real workflow, the user resolves in the same PR or opens a follow-up issue.
4. **The diary app is private.** Diffs and refinement docs cited above are real but live in a private repo; this file captures structure and counts.

## Stats

| Metric | Value |
| :--- | :--- |
| Total subagent invocations | 10 (3 refinement + 1 planner + 3 builder + 3 reviewer) |
| Wall-clock time end-to-end | ~50 minutes |
| Cross-lens conflicts surfaced | 6 (5 by planner + 1 by reviewer) |
| New tests | 13 (35 assertions) |
| Critical / High / Medium / Security findings | 0 / 1 / 6 / 0 |

## Lessons baked back into the agents

These v1→v2 lessons live in the refinement-agent prompts now:

1. **Cross-lens conflicts are inevitable.** Mandatory "What sibling agents must know" section in every refinement doc, with cross-impact tags on each defaulted decision.
2. **Existing-code interaction is the easiest thing to miss.** Architect now has a mandatory "Interaction with existing code" section; empty only if you've genuinely verified no overlap.
3. **Caps and limits are app-side rules, but they're UI surfaces.** UI agent now has mandatory "Limit and validation surfaces" and "Permissions surface" sections — every architect-defined cap and policy must be addressed.
4. **The phase planner is the conflict-resolution choke point.** Give it explicit authority to pick a default for trivial conflicts and document the choice in `phases.md`.
5. **Empty-input semantics must be deterministic at the scope/Action layer**, even when callers gate them. Document the choice prominently.
