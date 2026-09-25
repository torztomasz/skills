# Proxy benchmarks by platform

Reference for step 3 of [`SKILL.md`](SKILL.md). Prefer counts (left column) for the ratchet; use wall clock only to validate the proxy (step 4) and to report field results.

| Platform | Deterministic count | Wall clock validation |
|---|---|---|
| Node / TS CLI | `valgrind --tool=callgrind node --predictable script.js` (instructions), `node --cpu-prof` (call counts by function), `node --trace-module-loading` or `require` hook (modules loaded at startup) | `hyperfine --warmup 3 'cmd'` |
| Browser app | React commit count via `React.Profiler` `onRender`, `PerformanceObserver` for `layout-shift` and `longtask`, `MutationObserver` mutation count, style recalcs via Chrome tracing (`--enable-tracing` / DevTools Performance export, count `UpdateLayoutTree`) | `performance.mark/measure` between user action and usable result, Lighthouse only as a sanity check |
| Python CLI | `python -X importtime` (import cost tree), `cProfile` call counts, `perf stat -e instructions` | `hyperfine` |
| Native / Rust / Go | `perf stat -e instructions,cycles` (Linux), `valgrind --tool=cachegrind`, Go `-bench` with `-benchmem` allocs/op | `hyperfine`, `-benchtime` |
| Server endpoint | Query count per request, bytes serialized, allocations, DB rows scanned (`EXPLAIN ANALYZE`) | p75 from tracing spans |

## Making counts reproducible

- Pin inputs: fixture data at realistic size, seeded randomness, fixed clock.
- Production build, cold caches, same Node/browser version in CI as in the benchmark.
- `node --predictable` disables JIT concurrency and GC nondeterminism so instruction counts repeat exactly.
- Run three times; the ratchet compares the minimum.

## Ceiling file

One JSON or TOML file per repo, keyed by benchmark name, value = current ceiling. CI fails when measured > ceiling. Daily job: if measured < ceiling, write the new value and open a PR (or commit directly if the repo allows).

## Common findings worth checking first

These came from the claude.ai sprint and recur across codebases:

- **Work before first interaction.** A static pre-rendered shell lets the user act while the framework boots. Verify the static markup matches the hydrated render across viewports (pixel diff) and that input typed before handoff survives.
- **Non-Latin-1 strings.** One em dash or curly quote forces UTF-16 storage in V8 and slows every regex on that string; coerce to single-byte before heavy regex passes (e.g. syntax highlighting).
- **Layout shift with no owner.** Attribute each `layout-shift` entry to a region (sidebar, header, list) and rank by share of sessions affected.
- **Streaming/animation over budget.** Count frames above 8.3 ms or 16.6 ms; batch DOM writes per frame.
- **Startup import cost.** For CLIs, the biggest win is almost always lazy-loading modules the common command never touches.
