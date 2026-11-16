---
name: pr-screenshots
description: Before/after screenshot pairs and recordings in PR and issue descriptions. Use only when asked for before/after screenshots or a recording.
---

# PR screenshots

A reviewer should see what changed without checking out the branch: each
change gets one **pair** (before, after) side by side, with a caption right
below saying what it fixes.

## Layout

One table per change, caption directly under it:

```markdown
| Before | After |
| --- | --- |
| ![Before: Save button overflows the toolbar](./before-toolbar.png) | ![After: Save button wraps below the title](./after-toolbar.png) |

_Save button overflowed the toolbar on 375px screens; it now wraps below the title._
```

- **Caption**: one italic line. Name the user-visible problem, then the
  result, plus the conditions that trigger it (viewport, theme, state) when
  they matter. Write the symptom the reader sees ("overflowed the toolbar"),
  not the code change ("set `flex-wrap`").
- **Several changes**: repeat table + caption per change, in the order the
  description discusses them. Keeps each caption beside its own pair.
- **Responsive or themed change**: add rows to the same table, labelled in the
  first cell (`**375px**`, `**Dark**`), then one caption for the table.
- **Brand-new UI** (no before): a single image, no table, same caption line.
- **Interactions** (hover, animation, flow): record a short video or GIF for
  each side instead of a still; videos go in the same cells.

## Capturing a fair pair

The pair is only evidence when the two sides differ in the change alone:

- Same viewport size, zoom, theme, data, and app state on both sides.
- Everything the PR did not change looks identical: same table row order,
  same numbers, same placeholder text, same sidebar contents. Every stray
  difference reads as part of the change, so the reviewer can no longer tell
  what the PR did. Before uploading, compare the two frames region by region;
  if anything differs beyond the change itself, recapture. Usual causes: live
  data that moved between captures (capture both sides back to back against
  the same backend, or pin fixtures), a sort that follows that data, and
  browser state such as recently viewed or dismissed banners (use a fresh
  profile for both).
- Capture before on the base branch (or a `git stash` / worktree of it),
  after on the PR branch.
- Crop to the region that changed plus enough surroundings to locate it.
  Equal crop sizes keep the table columns equal width.
- Alt text reads as a standalone description (`Before: …` / `After: …`), so
  the pair still makes sense where images fail to load.
- Scrub tokens, emails, customer data and personal details from every frame
  before upload.

## Uploading

Upload with `gh`'s `--attach`; private-repo images committed to the repo or
linked by raw GitHub URL do not render, so never commit screenshots. Works on
`pr create`, `pr edit`, `pr comment`, `issue create`, `issue comment`:

```sh
gh pr edit 23 --body-file body.md \
  --attach './before-toolbar.png#Before: Save button overflows the toolbar' \
  --attach './after-toolbar.png#After: Save button wraps below the title'
```

`![alt](./file.png)` references in the body are rewritten to the uploaded
asset; attached files the body does not reference are appended at the end,
which breaks the side-by-side layout, so reference every file you attach.
Repeat `--attach` per file (up to 50). Videos render as a player.

Done when every visual change in the PR has a pair (or single image for new
UI) with a caption, and the rendered description shows each pair side by side.
