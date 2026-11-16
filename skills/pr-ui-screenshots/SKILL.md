---
name: pr-ui-screenshots
description: Use this skill when a change affects anything users see in the browser or app (layout, styling, components, copy, states) and the work will be included in a pull request. Covers capturing before/after screenshots on desktop and mobile viewports and adding them to the PR description.
---

Any pull request that changes the UI must include before/after screenshots for both desktop and mobile viewports in the PR description. Reviewers should be able to see the visual difference without checking out the branch.

## When to use

Use this skill when the change affects the rendered result:

- Layout, spacing, sizing, or positioning
- Colors, typography, icons, or imagery
- New, removed, or restyled components
- Visible copy, form hints, or button labels
- UI states (loading, empty, error, hover/focus where relevant)

Skip it for changes with no visible effect, such as backend logic, tests, build config, or refactors that leave the pixels identical. If you are unsure whether a change is visible, treat it as visible and include the screenshots.

## Steps

1. **Identify the affected views.** List the page(s) or screen(s) the change touches. If several, cover each one.
2. **Capture "before" screenshots** from the base branch (for example `main`). Run the app and screenshot the affected view(s) on desktop and on mobile.
3. **Capture "after" screenshots** from your working branch with the changes applied. Use the same route, viewport size, data, and UI state so the only difference is the change you made.
4. **Use consistent viewports.** Desktop around 1280-1440px wide, mobile around 375-390px wide. If the project has defined breakpoints or a design spec, use those instead.
5. **Match the conditions.** Same seed data, logged-in state, scroll position, and browser zoom for before and after. Crop or annotate only to highlight the changed area, never to hide regressions elsewhere.
6. **Upload the images and embed them in the PR body.** PR bodies cannot embed local file paths, but the GitHub CLI can attach files and rewrite your local references in the same command (requires `gh` 2.99.0+ — check with `gh --version`):
   1. Write the PR body to a file (for example `body.md` — keep it out of the repo or delete it afterwards; don't commit it) that references the screenshots with **relative** paths, such as `![Before - desktop](./before-desktop.png)`. Keep `body.md` and the screenshots in the same directory. Paths in `--body-file` are resolved relative to the file's directory (falling back to the working directory).
   2. Run the commands from inside the repository checkout (or pass `-R owner/repo` / set `GH_REPO`) — `gh` infers the repository from the local git repo and fails outside one; without a `<number>` argument it uses the PR for the current branch. Then: `gh pr edit <number> --body-file body.md --attach './before-desktop.png#Before - desktop' --attach './after-desktop.png#After - desktop'` (the `#Alt text` suffix is optional; up to 50 files per command). `gh` uploads each attachment, then rewrites the matching relative paths in the body to the uploaded asset URLs.
   3. Verify with `gh pr view <number> --json body --jq .body` that the `![...]` references now point at `github.com/user-attachments/assets/...` URLs.
   4. If some attachments fail, `gh` updates the PR with the ones that succeeded and exits non-zero. To recover, either re-run the same command with **all** `--attach` flags (already-uploaded files are re-uploaded and their refs rewritten), or first update `body.md` to point the succeeded files at their `github.com/user-attachments/assets/...` URLs (from `gh pr view --json body`), then re-run with only the missing files. Never re-run with only the missing files while `body.md` still has relative paths for the succeeded ones.
   5. Only if the installed `gh` has no attach support at all (check `gh pr edit --help` for an attach/upload flag), keep the files, add a placeholder in the PR description, and ask the user to drag the images in. Never claim image upload is impossible without checking `gh --version` and `gh pr edit --help` first — asking the user to drag images in when `gh` can do it is a failure, not a fallback.
7. **Add a "Screenshots" section to the PR description** using the layout below (with the relative paths from step 6 — `gh` rewrites them on upload). If you could not capture screenshots (app does not run, view is behind auth you do not have, and so on), say so explicitly in the PR instead of silently omitting them.

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

For multiple affected views, repeat the Desktop/Mobile tables under a subheading per view. For animations or interactions that a still frame cannot show, add a short GIF or screen recording instead of, or in addition to, the stills.

## Rules

- Never attach screenshots of the "after" state only. Before/after pairs on both viewports are the minimum.
- Never compress, resize, or watermark images to the point where detail is lost.
- Never commit screenshot images to the branch just to reference them, unless the repo already keeps PR assets that way.
- If the diff moves a view from broken to working (or vice versa), say so in words next to the pair.
- Update the screenshots whenever you push new commits that change the UI again. Stale screenshots are worse than none.
- Never work around a missing upload by committing screenshots to the branch or linking local file paths — unless the repo already keeps PR assets that way. Use the `gh pr edit --attach` flow above.
