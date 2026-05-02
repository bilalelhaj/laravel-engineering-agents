---
description: Stress-test a plan before implementation — assumes it failed in 6 months and works backward.
argument-hint: <path to plan — e.g. "docs/refinement/architecture.md" or feature description>
---

Dispatch `@laravel-premortem` on:

$ARGUMENTS

Sets the frame *"it is 6 months from now, this plan has failed"*, generates failure scenarios, spawns parallel deep-dive agents per scenario, synthesizes most-likely / most-dangerous / hidden assumption / revised plan / pre-impl checklist. Best run between refinement and phase-planning, or before any major migration. Output: `docs/premortem-{feature-slug}.md`.

Based on Gary Klein's premortem method (HBR 2007).
