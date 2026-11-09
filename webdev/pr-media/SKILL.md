---
name: pr-media
description: Use when putting screenshots, before/after images or screen recordings into a GitHub PR or issue (description or comment).
---
# Images and videos in PRs and issues

`gh` (≥ 2.103) uploads them itself. `gh pr create|edit|comment` and `gh issue create|edit|comment` take
`--attach './file.png#alt text'`; repeat the flag for several files (max 50 per command).
Place them by referencing `![alt](./file.png)` in the `--body-file`; unreferenced ones are appended.

- **Write access to the repo:** always use `--attach`. Don't use agent-browser or ask anyone to upload by hand.
- **No write access** (e.g. a fork PR against someone else's repo, which fails with "attaching files requires write access"):
  save the images in `/tmp` and hand the paths to the PR author to drop in themselves.
- Never use throwaway branches or commits to host images.
