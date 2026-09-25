# perf-ratchet

Measurement-driven performance work for any app, website or CLI, distilled from Anthropic's post [How we made claude.ai 3x faster in two weeks](https://claude.dev/blog/how-we-made-claude-ai-faster/).

The transferable part of that sprint is a loop, not a bag of tricks:

1. Take a user journey ("scrolling the feed", "CLI startup to first output") and measure it from user action to usable result.
2. Baseline it in the field.
3. Build a deterministic proxy benchmark (instruction counts, framework commits, DOM mutations, imported modules) because wall clock is noisy.
4. Prove the proxy tracks wall clock before trusting it.
5. Ratchet it: the proxy becomes a CI ceiling that fails PRs which raise it, and a daily job lowers the ceiling on every win.
6. Optimize in small PRs, tests first, flags on anything user-visible, staged rollout. Stay in one journey until its field number moves.

The name is the mechanism: a ratchet only turns one way, so wins become permanent floors.

## Use

```
/perf-ratchet scrolling the dashboard
```

The journey is the input. If you name a function instead of something a user does, the skill asks which journey it belongs to and waits.

## Files

- `SKILL.md`: the loop, the completion criterion for each step, and the judgement calls (complexity budget, brave proposals, frame budgets).
- `BENCHMARKS.md`: proxy benchmark tooling per platform (Node, browser, Python, native, server) and the ceiling-file convention. Loaded only at step 3.
