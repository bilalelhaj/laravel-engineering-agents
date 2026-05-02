---
description: Independent audit of pending changes — N+1, fat controllers, security, Filament version drift, production readiness.
argument-hint: <optional scope — e.g. "the current PR" or "phase 3" or "the diff against main">
---

Dispatch `@laravel-reviewer` on:

$ARGUMENTS

Default scope: pending changes in the working tree (`git diff main...HEAD` or unstaged changes). Read-only audit — produces findings with severity (Critical / High / Medium / Security / Production-readiness), file:line, and concrete fixes.

If the diff touches `app/Filament/`, `resources/views/filament/`, or `app/Providers/Filament/*`, also dispatch `@filament-reviewer` in parallel for Filament-specific issues.

For deeper audits, escalate to `@laravel-devops` (CI/CD/Docker), `@laravel-perf` (caching/queues/observability), or `@laravel-security` (auth/crypto/headers/CVE/GDPR).
