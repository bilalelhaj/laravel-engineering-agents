---
description: Build a Laravel feature end-to-end via the orchestrator. Refinement → planning → build → review.
argument-hint: <feature description or path to spec>
---

Run the full Laravel-engineering-agents pipeline on the feature specified by the user:

$ARGUMENTS

Use `@laravel-orchestrator` to drive it. Read `docs/project-description.md` if it exists; otherwise the description above is the spec. Detect Filament from `composer.json` and include `@filament-architect` in refinement if installed. Stop and surface to me on any Critical or High reviewer findings.
