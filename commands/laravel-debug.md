---
description: Diagnose a failing test or production error — hypothesis-driven, hands you a minimal patch.
argument-hint: <test name or error description — e.g. "tests/Feature/OrderTest::it ships an order" or "the exception in storage/logs/laravel.log">
---

Dispatch `@laravel-debugger` on:

$ARGUMENTS

Forms 3–5 hypotheses, ranks by cost-to-test, runs targeted commands to confirm or reject each, narrows to one confirmed cause, hands back a minimal patch description with side-effect analysis. **Read-only** — does not implement the fix.

If you want the fix applied after diagnosis: ask `@laravel-builder` to apply the debug report.
