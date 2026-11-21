---
name: pr-ui-screenshots
description: Use this skill when a change affects anything users see in the browser or app (layout, styling, components, copy, states) and the work will be included in a pull request. Covers capturing before/after screenshots on desktop and mobile viewports and adding them to the PR description.
---

Every PR that changes what users see carries before/after screenshots, for desktop and mobile, in its description. Reviewers see the difference without checking out the branch.

Applies to anything rendered: layout, spacing, colours, typography, components, visible copy, UI states. Skip backend logic, tests, build config and refactors that leave the pixels identical. When unsure, treat the change as visible.

## Steps

1. **List the affected views** and cover each one.
2. **Capture "before" on the base branch** (for example `main`), then **"after" on the working branch**. Keep route, data, login state, scroll and zoom identical, so the change is the only difference. Use whatever browser tooling is available (Playwright, a preview tool).
3. **Use consistent viewports**: desktop 1280-1440px wide, mobile 375-390px. The project's own breakpoints take precedence.
4. **Keep full detail.** Crop or annotate only to highlight the change, never to hide regressions elsewhere.
5. **Upload and embed** the images (below).
6. **Add the Screenshots section** (layout below). If capture is impossible (the app won't run, the view is behind auth you lack), say so in the PR.

## Upload with `gh`

`gh pr edit --attach` uploads local images and rewrites relative paths in the PR body to hosted URLs in one command.

1. **Check support**: `gh pr edit --help | grep attach`. Without it, upgrade `gh` first (distro packages are often too old) by installing the latest release into a bin directory on `PATH`.
2. **Write the body to `body.md`**, in the same directory as the images, referencing them by relative path (`![Before - desktop](./before-desktop.png)`). Keep `body.md` out of the commit.
3. **Run from inside the repo checkout** (or pass `-R owner/repo`):
   `gh pr edit <number> --body-file body.md --attach './before-desktop.png#Before - desktop' --attach './after-desktop.png#After - desktop'`
   The `#Alt text` suffix is optional; up to 50 files per command.
4. **Verify**: `gh pr view <number> --json body --jq .body` shows `github.com/user-attachments/assets/...` URLs.
5. **On partial failure**, `gh` updates the PR with the uploads that succeeded and exits non-zero. Re-run with **all** `--attach` flags.
6. **If `gh` cannot be upgraded**, keep the files, add a placeholder in the description, and ask the user to drag the images in.

Images stay out of the branch, unless the repo already keeps PR assets that way.

## PR description layout

```markdown
## Screenshots

### Desktop
| Before | After |
| --- | --- |
| ![Before - desktop](./before-desktop.png) | ![After - desktop](./after-desktop.png) |

### Mobile
| Before | After |
| --- | --- |
| ![Before - mobile](./before-mobile.png) | ![After - mobile](./after-mobile.png) |
```

For several views, repeat the tables under a subheading per view. For motion a still can't show, add a short GIF or recording.

## Rules

- A before/after pair on both viewports is the minimum.
- When the diff moves a view from broken to working (or the reverse), say so beside the pair.
- Refresh the screenshots after every push that changes the UI again; stale screenshots mislead.
