---
name: browser-ui-check
description: Use this skill when you need to see, verify, or test UI in a browser, for example to screenshot a dev server or local page, check rendering, compare before/after states, or script clicks, typing and assertions with Playwright. Skip it for backend, API and unit-test work.
---

Drive headless Chromium with Playwright, screenshot the result, then look at the PNG.

## Set up

Use the project's own Playwright (`npx playwright --version`). If it has none, install it in a scratch directory outside the repo.

1. **Browser**: `npx playwright install chromium`.
2. **System libraries and fonts**: `npx playwright install-deps chromium` (needs root and `apt`).
   `error while loading shared libraries: libX.so` means this step is missing.
3. **Containers**: bake step 2 into the image, since packages installed at runtime vanish when the container is recreated. Point `PLAYWRIGHT_BROWSERS_PATH` at a persistent volume so step 1 survives too.

## Capture

```js
const { chromium } = require('playwright');
const browser = await chromium.launch();
try {
  const page = await browser.newPage({ viewport: { width: 1280, height: 800 } });
  await page.goto('http://localhost:3000', { waitUntil: 'networkidle' });
  await page.screenshot({ path: 'ui.png', fullPage: true });
} finally {
  await browser.close();
}
```

- Run as root without extra flags: Playwright already launches Chromium without its sandbox.
- WebGL pages need software rendering: `chromium.launch({ args: ['--use-gl=angle', '--use-angle=swiftshader', '--enable-unsafe-swiftshader'] })`. It is CPU-heavy, so keep runs short.
- Start a dev server for the script and stop it in the same `finally`. A crashed script otherwise leaves the server (and CPU load) behind; check `ps` afterwards.

## Read the result

- View the PNG with an image-capable file reader. When you cannot view images, say so, and verify layout through the DOM (`getBoundingClientRect`, `getComputedStyle`) rather than judging looks.
- Drive interaction with `page.click`, `page.fill` and `expect`-style asserts instead of eyeballing HTML or CSS.

## Troubleshooting

- `Executable doesn't exist`: run step 1 and check that `PLAYWRIGHT_BROWSERS_PATH` matches where the browser was installed.
- No display is available: stay headless. `xvfb-run -a` works only when `xvfb` is installed.
- T3's built-in `preview_*` tools run Chrome through the T3 server. As root they need `T3CODE_SERVER_BROWSER_SANDBOX=0` in the server environment, and `t3 browser setup` when libraries are missing.
