---
name: perf-ratchet
description: Measurement-driven performance work for any app, website or CLI. Use when asked to make a specific user journey faster, cut latency or startup time, fix jank or layout shift, or stop performance regressions. Not for a single known-slow function with an obvious fix.
argument-hint: <journey to speed up, e.g. "CLI startup" or "opening a conversation">
---

# Perf ratchet

Performance work as a ratchet, not a one-off: take a user-named **journey**, find a **proxy** metric that is deterministic, prove the proxy tracks wall clock, then turn it into a CI ceiling that only moves down. Distilled from Anthropic's claude.ai sprint (3x faster in two weeks, 3000+ merged changes, zero rollbacks). Their key principle: **measuring something makes it tractable**. Unmeasured perf work is guesswork; measured perf work is hill climbing with immediate feedback.

Platform-specific tooling (how to get instruction counts, commit counts, layout shift, import time) lives in [`BENCHMARKS.md`](BENCHMARKS.md). Read it at step 3.

## The loop

Run this for the one journey given. Finish it before proposing another.

### 1. Take the journey from the user

The **journey** is the input to this skill, never something you choose. It is a thing the user does, described from their side: "opening a conversation", "CLI startup to first output", "submitting the form". Read it from the invocation argument or the request. If none is given, or the request names a function or file instead of a user action, ask one question ("Which user journey should get faster?") and stop until answered. Hotspots come later, and only inside this journey.

Turn the journey into a measurement that:

- **starts on a user action** (click, keypress, process spawn) and **ends when the result is usable** (rendered, interactive, output flushed), never at an internal milestone;
- **separates client from server time**, so a fix lands on the side that actually owns the cost.

Done when the journey has a named measurement with defined start and end events, confirmed with the user in one line.

### 2. Baseline in the field

Get the p75 for the measurement from real users, or add instrumentation (`performance.mark`/`measure`, spans, timestamps on stdout) and wait for data. Record the number: it is the only proof of success at the end. Done when the baseline is written down.

### 3. Build a proxy benchmark

Wall clock is noisy; counts are stable. For the journey's hot path, build a lab benchmark that reports a **count**: CPU instructions, function calls, framework commits, style recalculations, DOM mutations, imported modules, allocations. Reproduce the slow path exactly as a user hits it (cold cache, real data size, production build). See [`BENCHMARKS.md`](BENCHMARKS.md) for tooling per platform.

Done when the benchmark runs in under a minute and gives the same number on repeated runs.

### 4. Prove the proxy before trusting it

Treat every new benchmark with skepticism. Make one change that moves the count, then measure wall clock for the same change. Adopt the proxy only when both move in the same direction. A proxy that moves without wall clock following is a dead end; drop it and pick another count.

Done when a written note links proxy delta to wall clock delta (e.g. "instructions −48%, wall clock −78%").

### 5. Ratchet it in CI

The proxy becomes a ceiling: a PR that raises the count fails CI. A daily job lowers each ceiling to the current best whenever the count drops. This converts every win into a permanent floor and every regression into a red build the same day.

Done when the ceiling file is committed and the failing case is demonstrated once.

### 6. Optimize in small, flagged PRs

For each candidate fix, in this order:

1. **Tests first.** Behaviour tests for the path you are about to change, before touching it. Optimizations change semantics silently; the tests are what let you move fast.
2. **Smallest PR that moves the proxy.** Several small PRs beat one large one. Size each for review risk.
3. **Flag anything user-visible.** Short-lived feature flag, staged rollout (internal → 1% → all). Retire the flag as soon as the change is proven; a flag left behind is debt.
4. **Watch the field**, not just the lab. If p75 moves, ratchet the ceiling down. If it does not, the proxy lied for this path: go back to step 4.

Then pick the next largest cost **within the same journey** and repeat. When the field number has moved and the ratchet holds it, report and stop; the user picks the next journey.

## Judgement calls

- **Complexity budget.** A win must pay for its maintenance. "2 ms per action is not worth a custom build plugin" is a valid verdict; say it and close the PR.
- **Be brave in proposals, careful in shipping.** Propose the structural fix (pre-rendered static shell, moving work off the critical path, single-byte string coercion before regex) rather than the safe micro-tweak, then land it in guarded slices.
- **Lab misses what the field catches.** Speculative prerender, odd viewports, slow devices. When a field anomaly appears, add the exact check that would have caught it (e.g. pixel alignment across N viewports) so the class of bug stays caught.
- **Time budgets, not vibes, for smoothness.** Streaming or animation work gets a frame budget (16.6 ms at 60 Hz, 8.3 ms at 120 Hz) and a nightly job that reports frames over budget.

## Report

End with the baseline beside the new number for the journey, the list of live ceilings, any flags still to retire, and at most three candidate journeys the user might name next.
