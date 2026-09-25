# skills

Claude Code skills I use and maintain. Each lives in `skills/<name>/` with a `SKILL.md` and an optional README.

| Skill | What it does |
|---|---|
| [perf-ratchet](skills/perf-ratchet) | Measurement-driven performance loop from Anthropic's "3x faster in two weeks" post. Name a user journey, build a proxy benchmark, ratchet it into CI. |

## Install

With the [skills CLI](https://github.com/vercel-labs/skills) from Vercel:

```sh
npx skills add torztomasz/skills --skill perf-ratchet -g
```

Drop `-g` to install into the current project instead of your user-level skills. Add `--list` to see every skill in this repo before choosing.

Or copy a skill by hand:

```sh
git clone https://github.com/torztomasz/skills /tmp/skills
cp -r /tmp/skills/skills/perf-ratchet ~/.claude/skills/
```

## License

MIT
