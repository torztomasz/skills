# skills

Agent skills I use and maintain. Each lives in `skills/<name>/` with a `SKILL.md` and an optional README. The format is provider-independent: any agent that reads `SKILL.md` (Claude Code, Codex, Cursor, Copilot, Gemini CLI and others) can use them.

| Skill | What it does |
|---|---|
| [perf-ratchet](skills/perf-ratchet) | Measurement-driven performance loop from Anthropic's "3x faster in two weeks" post. Name a user journey, build a proxy benchmark, ratchet it into CI. |

## Install

With the [skills CLI](https://github.com/vercel-labs/skills) from Vercel:

```sh
npx skills add torztomasz/skills --skill perf-ratchet -g
```

The CLI detects which agents you have and installs to each of them. Drop `-g` to install into the current project instead of your user-level skills. Add `--list` to see every skill in this repo before choosing, or `--agent <name>` to target one agent.

Or copy a skill folder by hand into your agent's skills directory (for example `~/.claude/skills/`, `~/.codex/skills/` or `.cursor/skills/`).

## License

MIT
