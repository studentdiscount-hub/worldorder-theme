# Dead Asset Removal — Design Doc
**Date:** 2026-03-24
**Status:** Approved

## Problem

Two assets are loading on live pages without serving any purpose:

1. **`sparkle.gif` (176KB)** — Defined as a CSS custom property `--sparkle` in `base.css`, but `var(--sparkle)` is never consumed by any selector. The related `--easter-egg` animation feature (a Dawn theme holdover) is permanently set to `none` and no JavaScript activates it. The variable and file are dead weight.

2. **`pagefly-animation.css` (17KB)** — Loaded via `pagefly-main-css.liquid` on any page whose template suffix starts with `pf-`. The two remaining `pf-` templates (`page.pf-007fd637`, `page.pf-f0a79691`) now use standard Shopify sections — not PageFly — so the full animation library (60+ keyframe animations: bounce, wobble, zoom, etc.) loads on those pages for no reason. Confirmed: PageFly animations are not used on any live page.

## Approved Design — Option A (surgical)

### Task 1: sparkle.gif
- Remove `--sparkle: url('./sparkle.gif');` from `base.css` (the `:root` block at ~line 3448)
- Delete `assets/sparkle.gif` from the theme

### Task 2: pagefly-animation.css
- Remove the single `<link>` tag for `pagefly-animation.css` from `snippets/pagefly-main-css.liquid`
- Leave all other PageFly structural CSS in place (grid, layout reset, colour schemes) — safe for any remaining PageFly content

## What Is NOT Changed
- The `--easter-egg` variable and 3D card hover animation CSS remain untouched (no visual regression)
- `pagefly-main-css.liquid` itself stays; only the animation stylesheet line is removed
- No templates, routes, or admin settings are affected
