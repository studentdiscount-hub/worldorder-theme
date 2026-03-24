# Dead Asset Removal Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Remove two dead assets — `sparkle.gif` and `pagefly-animation.css` — that occupy bandwidth and CDN space without serving any function on the live site.

**Architecture:** Two surgical edits. Task 1 strips a never-consumed CSS custom property from `base.css` and deletes the orphaned GIF. Task 2 removes a single `<link>` tag from `pagefly-main-css.liquid` that loads 17KB of unused animation keyframes. No JavaScript changes, no template changes, no visual regressions expected.

**Tech Stack:** Shopify Liquid, CSS, Bash (for file deletion and grep verification)

---

### Task 1: Remove `--sparkle` from base.css and delete sparkle.gif

**Files:**
- Modify: `assets/base.css:3446-3450`
- Delete: `assets/sparkle.gif`

**Step 1: Verify the exact block to edit**

```bash
sed -n '3444,3452p' assets/base.css
```

Expected output:
```
  }

  :root {
    --easter-egg: none;
    --sparkle: url('./sparkle.gif');
  }
```

**Step 2: Remove the `--sparkle` line from base.css**

Edit `assets/base.css`. Find the `:root` block at line ~3446 and remove **only** the `--sparkle` line, leaving `--easter-egg: none;` intact:

Before:
```css
  :root {
    --easter-egg: none;
    --sparkle: url('./sparkle.gif');
  }
```

After:
```css
  :root {
    --easter-egg: none;
  }
```

**Step 3: Verify the edit**

```bash
sed -n '3444,3451p' assets/base.css
```

Expected: no `--sparkle` line present. `--easter-egg: none;` still present.

**Step 4: Verify `--sparkle` has zero remaining references**

```bash
grep -rn "\-\-sparkle\|sparkle\.gif" . --include="*.css" --include="*.liquid" --include="*.js" --include="*.json"
```

Expected: no matches (or only matches inside this plan doc itself).

**Step 5: Delete sparkle.gif**

```bash
rm assets/sparkle.gif
```

**Step 6: Confirm deletion**

```bash
ls assets/sparkle.gif 2>&1
```

Expected: `No such file or directory`

**Step 7: Commit**

```bash
git add assets/base.css
git add -u assets/sparkle.gif
git commit -m "perf: remove unused --sparkle variable and sparkle.gif (176KB dead asset)"
```

---

### Task 2: Remove pagefly-animation.css from pagefly-main-css.liquid

**Files:**
- Modify: `snippets/pagefly-main-css.liquid:10`

**Step 1: Verify the exact line to remove**

```bash
grep -n "pagefly-animation" snippets/pagefly-main-css.liquid
```

Expected:
```
10:<link rel="stylesheet" href="{{ 'pagefly-animation.css' | asset_url }}" media="print" onload="this.media='all'">
```

**Step 2: Remove the animation CSS link**

Edit `snippets/pagefly-main-css.liquid`. Delete line 10:

```html
<link rel="stylesheet" href="{{ 'pagefly-animation.css' | asset_url }}" media="print" onload="this.media='all'">
```

Leave all other `<style>` blocks in the file completely untouched.

**Step 3: Verify the line is gone**

```bash
grep -n "pagefly-animation" snippets/pagefly-main-css.liquid
```

Expected: no output (zero matches).

**Step 4: Verify the file still has its other content**

```bash
wc -l snippets/pagefly-main-css.liquid
grep -c "<style>" snippets/pagefly-main-css.liquid
```

Expected: file has fewer lines by exactly 1; still contains multiple `<style>` blocks.

**Step 5: Confirm pagefly-animation.css has no other load points**

```bash
grep -rn "pagefly-animation" . --include="*.liquid" --include="*.json"
```

Expected: no matches.

**Step 6: Commit**

```bash
git add snippets/pagefly-main-css.liquid
git commit -m "perf: remove pagefly-animation.css (17KB unused animation library)"
```

---

### Task 3: Final verification

**Step 1: Confirm no regressions in dead-code targets**

```bash
grep -rn "sparkle" . --include="*.css" --include="*.liquid"
grep -rn "pagefly-animation" . --include="*.liquid"
```

Expected: zero matches on both.

**Step 2: Confirm base.css still loads correctly**

```bash
grep -n "base.css" layout/theme.liquid
grep -n "\-\-easter-egg" assets/base.css
```

Expected: `base.css` still referenced in theme.liquid; `--easter-egg: none;` still present in base.css.

**Step 3: Confirm pagefly structural CSS is intact**

```bash
grep -c "<style>" snippets/pagefly-main-css.liquid
```

Expected: 4 (the four inline `<style>` blocks remain).
