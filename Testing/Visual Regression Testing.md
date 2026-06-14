---
id: a1b2c3d4-1168-4000-8000-000000000168
title: Visual Regression Testing
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001168
---
# Visual Regression Testing

Visual regression testing compares screenshots of UI components to detect unintended visual changes. It catches CSS issues, layout shifts, and rendering differences.

## How It Works

```
Baseline screenshot ──► Compare ──► Diff image ──► Pass/Fail
                               │
Current screenshot ───────────┘

Pixel comparison:
  - Same pixels → pass
  - Different pixels → fail (or accept as new baseline)
```

## Types of Visual Testing

| Type | Scope | Speed | Use Case |
|------|-------|-------|----------|
| Component-level | Single component | Fast (millions) | Component library, design system |
| Page-level | Full page | Medium | E2E visual checks |
| Viewport-level | Specific breakpoints | Medium | Responsive design |
| Element-level | Specific element | Fast | Important UI elements |
| Story-level | Stories/permutations | Fast | Storybook integration |

## Tools

| Tool | Approach | Platform |
|------|----------|----------|
| Percy | Cloud-based, cross-browser | SaaS |
| Chromatic | Storybook integration | SaaS |
| Applitools | AI-powered (layout, content) | SaaS |
| Playwright | Built-in visual comparisons | Open source |
| Cypress | Plugin (cypress-image-snapshot) | Open source |
| BackstopJS | Puppeteer-based | Open source |
| Pixelmatch | Low-level pixel comparison | Library |
| Resemble.js | Image comparison library | Library |

## Playwright Visual Testing

```javascript
import { test, expect } from "@playwright/test";

test("homepage matches snapshot", async ({ page }) => {
    await page.goto("https://example.com");
    await expect(page).toHaveScreenshot("homepage.png", {
        fullPage: true,
        maxDiffPixels: 100,
    });
});

test("login form looks correct", async ({ page }) => {
    await page.goto("https://example.com/login");
    const form = page.locator(".login-form");
    await expect(form).toHaveScreenshot("login-form.png");
});
```

```bash
# Update snapshots
npx playwright test --update-snapshots

# CI mode (fail if no snapshot exists)
npx playwright test
```

## Percy Integration

```javascript
// @percy/cypress
describe("Visual tests", () => {
    it("should match homepage snapshot", () => {
        cy.visit("/");
        cy.percySnapshot("Homepage");
    });

    it("should match mobile view", () => {
        cy.viewport(375, 812);
        cy.visit("/");
        cy.percySnapshot("Homepage Mobile");
    });
});
```

```yaml
# .percy.yml
version: 2
snapshot:
  widths:
    - 375
    - 1280
    - 1920
  minHeight: 1024
  percyCSS: |
    /* Hide dynamic content */
    .ad-banner { visibility: hidden; }
    .timestamp { display: none; }
```

## Chromatic (Storybook)

```javascript
// stories/Button.stories.js
export default {
    title: "Components/Button",
    component: Button,
    parameters: {
        chromatic: {
            disableSnapshot: false,
            viewports: [375, 1280],
            pauseAnimationAtEnd: true,
        },
    },
};

export const Primary = {
    args: {
        variant: "primary",
        label: "Click me",
        disabled: false,
    },
};

export const Disabled = {
    args: {
        variant: "primary",
        label: "Click me",
        disabled: true,
    },
};
```

## Handling Dynamic Content

```javascript
// Freeze animations and async content before snapshot
async function stabilizePage(page) {
    // Freeze CSS animations
    await page.addStyleTag({
        content: `*, *::before, *::after {
            animation-duration: 0s !important;
            transition-duration: 0s !important;
        }`,
    });

    // Wait for fonts
    await page.waitForFunction(() => document.fonts.ready);

    // Wait for images
    await page.waitForFunction(() =>
        Array.from(document.images).every((img) => img.complete)
    );

    // Wait for network idle
    await page.waitForLoadState("networkidle");
}
```

## Pixel Difference Threshold

```javascript
// Tolerance settings
const config = {
    // Pixel matching
    mismatchThreshold: 0.01,      // 1% of pixels can differ
    failureThreshold: 50,         // Max number of different pixels
    failureThresholdType: "pixel", // "percent" or "pixel"

    // Anti-aliasing tolerance
    diffColor: [255, 0, 0],       // Red highlight for differences
    transparent: {
        red: 0,
        green: 255,
        blue: 0,
        alpha: 0.5,
    },
};
```

## CI Integration

```yaml
# GitHub Actions
name: Visual Tests
on: [pull_request]

jobs:
  visual:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npx playwright install --with-deps
      - run: npm run build
      - run: npx percy exec -- npx playwright test
        env:
          PERCY_TOKEN: ${{ secrets.PERCY_TOKEN }}

      - name: Check for visual diffs
        if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: visual-diffs
          path: test-results/
```

## Best Practices

| Practice | Why |
|----------|-----|
| Component-level over page-level | Faster, more targeted diffs |
| Freeze animations and dates | Eliminate false positives |
| Use dedicated test data | Avoid environment-dependent changes |
| Review diffs in PRs | Catch intentional vs accidental changes |
| Set appropriate thresholds | Too strict = noise, too loose = miss bugs |
| Test at multiple viewports | Catch responsive layout issues |
| Run on CI with baseline comparison | Prevent visual regressions from merging |
