# pr-screenshots

Puts before/after screenshot pairs and recordings into PR and issue descriptions so a reviewer sees what
changed without checking out the branch: one `| Before | After |` table per change with an italic caption
under it, captured fairly (same viewport, theme, data and state on both sides), cropped to the change, and
uploaded with `gh --attach` so images render in private repos too.

The table layout is the one [Peekaboo](https://github.com/torztomasz/peekaboo) reads: with that Chrome
extension installed, reviewers can flip, fade or pixel-diff each pair right on the PR page.

## Install

```sh
npx skills add torztomasz/skills --skill pr-screenshots -g
```

## Use

Ask your agent for before/after screenshots (or a recording) on a PR or issue. The skill only triggers on
that request, so it stays out of the way otherwise.
