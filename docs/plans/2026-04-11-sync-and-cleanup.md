# Sync Live Site → GitHub & Dead Code Cleanup

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Land all session changes into git, sync the repo with the live-patched theme directory, delete dead sections and legacy templates, then push a clean `main` to GitHub.

**Architecture:** The repo (`worldorder-repo`) is the git-tracked source; `worldorder-v2-live-patched` is the Shopify-ready directory (always synced post-edit). They have drifted. The fix/site-audit-issues branch is mid-rebase. We will: finish the rebase with our session changes folded in → sync all drifted files → delete dead code → merge to main → push.

**Tech Stack:** Git, Shopify CLI (not needed for this plan), Bash file copy

---

## Context & Current State

```
Repo path:       worldorder-repo/
Live-patch path: worldorder-v2-live-patched/
Branch:          fix/site-audit-issues  (currently mid-rebase onto 6ed0348)
Rebase state:    Paused at edit step for commit 27c1dde
                 One commit remaining to replay: 99949c1
Unstaged changes (session work):
  - assets/wo-v2.css           (major: --wo-muted, Dawn vars, banner/facets/card CSS, portrait ratio)
  - sections/canvas-page.liquid (complete redesign — transmission aesthetic)
  - sections/wo-about-content.liquid (1-line: hidden arrow placeholder fix, already in live-patched)
```

### Dead Code Identified

| File | Reason |
|------|--------|
| `sections/wo-editorial-strip.liquid` | Never referenced in any template |
| `sections/wo-footer.liquid` | Never referenced in any template (footer uses Dawn sections) |
| `sections/wo-manifesto-band.liquid` | Never referenced in any template |
| `templates/page.about-world-order.liquid` | Replaced by `page.wo-about.json` + `wo-about-content.liquid` |
| `templates/page.canvas.json` | References wrong section type (`wo-radio-content`); canvas now uses `canvas-page.liquid` section; not present in live-patched |

### Files That Differ (repo vs live-patched)

These files exist in both but have diverged — live-patched is the source of truth:

- `assets/global.js`
- `layout/theme.liquid`
- `sections/footer-group.json`
- `sections/header.liquid`
- `sections/main-product.liquid`
- `sections/wo-hero.liquid`
- `sections/wo-products-strip.liquid`
- `sections/wo-radio-content.liquid`
- `templates/index.json`
- `templates/page.json`
- `templates/page.wo-radio.json`

File only in repo (not in live-patched): `assets/wo-logo.svg` — keep it, it's referenced in header.

---

## Task 1: Stage Session Changes and Complete the Rebase

**Files:**
- Modify (stage): `assets/wo-v2.css`
- Modify (stage): `sections/canvas-page.liquid`
- Modify (stage): `sections/wo-about-content.liquid`

**Step 1: Stage the three modified files**

```bash
cd "/Users/jordansimo/Documents/Claude/Projects/world order/worldorder-repo"
git add assets/wo-v2.css sections/canvas-page.liquid sections/wo-about-content.liquid
```

**Step 2: Verify they are staged**

```bash
git status
```

Expected: all three appear under "Changes to be committed", nothing under "Changes not staged".

**Step 3: Create a new commit on top of the current rebase HEAD**

```bash
git commit -m "fix: catalog fonts, portrait product ratio, coords visibility, canvas redesign"
```

Expected: commit succeeds, SHA is printed.

**Step 4: Continue the rebase to replay the final commit**

```bash
git rebase --continue
```

Expected: Either replays `99949c1` cleanly and prints "Successfully rebased…", OR pauses with a conflict. If a conflict appears, proceed to Step 5. If clean, skip to Step 6.

**Step 5 (conflict path): Resolve any conflicts, then continue**

If conflicts appear:
```bash
git status          # see which files conflict
# For each conflicted file, open it and keep the version that is correct.
# Rule of thumb: if the conflict is in a WO custom file, keep "ours" (HEAD).
# If it's a Shopify-generated JSON template, keep "theirs" unless it overwrites intentional WO changes.
git add <resolved-files>
git rebase --continue
```

**Step 6: Verify we are back on the branch**

```bash
git branch
git log --oneline -5
```

Expected: `* fix/site-audit-issues` is active. The log shows commits from this session.

**Step 7: Commit**

Already committed in Step 3. No additional commit needed.

---

## Task 2: Sync Drifted Files from Live-Patched into Repo

**Files:**
- Modify: all files listed in "Files That Differ" above

**Step 1: Copy each drifted file**

```bash
BASE_LIVE="/Users/jordansimo/Documents/Claude/Projects/world order/worldorder-v2-live-patched"
BASE_REPO="/Users/jordansimo/Documents/Claude/Projects/world order/worldorder-repo"

cp "$BASE_LIVE/assets/global.js"                    "$BASE_REPO/assets/global.js"
cp "$BASE_LIVE/layout/theme.liquid"                 "$BASE_REPO/layout/theme.liquid"
cp "$BASE_LIVE/sections/footer-group.json"          "$BASE_REPO/sections/footer-group.json"
cp "$BASE_LIVE/sections/header.liquid"              "$BASE_REPO/sections/header.liquid"
cp "$BASE_LIVE/sections/main-product.liquid"        "$BASE_REPO/sections/main-product.liquid"
cp "$BASE_LIVE/sections/wo-hero.liquid"             "$BASE_REPO/sections/wo-hero.liquid"
cp "$BASE_LIVE/sections/wo-products-strip.liquid"   "$BASE_REPO/sections/wo-products-strip.liquid"
cp "$BASE_LIVE/sections/wo-radio-content.liquid"    "$BASE_REPO/sections/wo-radio-content.liquid"
cp "$BASE_LIVE/templates/index.json"                "$BASE_REPO/templates/index.json"
cp "$BASE_LIVE/templates/page.json"                 "$BASE_REPO/templates/page.json"
cp "$BASE_LIVE/templates/page.wo-radio.json"        "$BASE_REPO/templates/page.wo-radio.json"
```

**Step 2: Verify the diffs are gone**

```bash
diff -rq --exclude=".git" --exclude="docs" --exclude="*.svg" \
  "$BASE_LIVE/" "$BASE_REPO/" 2>/dev/null | grep -v ".gitignore\|wo-logo\|page.canvas\|page.about-world-order\|wo-footer\|wo-editorial\|wo-manifesto"
```

Expected: No output (no remaining diffs on synced files).

**Step 3: Stage and commit**

```bash
cd "$BASE_REPO"
git add assets/global.js layout/theme.liquid sections/footer-group.json \
  sections/header.liquid sections/main-product.liquid sections/wo-hero.liquid \
  sections/wo-products-strip.liquid sections/wo-radio-content.liquid \
  templates/index.json templates/page.json templates/page.wo-radio.json
git commit -m "chore: sync repo with live-patched theme (layout, header, product, radio, templates)"
```

---

## Task 3: Delete Dead Section Files

**Files:**
- Delete: `sections/wo-editorial-strip.liquid`
- Delete: `sections/wo-footer.liquid`
- Delete: `sections/wo-manifesto-band.liquid`

**Step 1: Verify these sections are truly unused**

```bash
cd "/Users/jordansimo/Documents/Claude/Projects/world order/worldorder-repo"
grep -r "wo-editorial-strip\|wo-footer\|wo-manifesto-band" templates/ sections/ layout/
```

Expected: No output. If any references appear, do NOT delete that file — investigate first.

**Step 2: Delete the dead sections**

```bash
git rm sections/wo-editorial-strip.liquid sections/wo-footer.liquid sections/wo-manifesto-band.liquid
```

**Step 3: Commit**

```bash
git commit -m "chore: remove dead sections — editorial-strip, footer, manifesto-band (unreferenced)"
```

---

## Task 4: Delete Legacy Templates

**Files:**
- Delete: `templates/page.about-world-order.liquid`
- Delete: `templates/page.canvas.json`

**Step 1: Confirm page.about-world-order is not assigned in Shopify admin**

This is a Shopify admin check — cannot be automated. Confirm verbally with Jordan that no live page uses the `page.about-world-order` template before deleting.

**Step 2: Delete the legacy templates**

```bash
cd "/Users/jordansimo/Documents/Claude/Projects/world order/worldorder-repo"
git rm templates/page.about-world-order.liquid templates/page.canvas.json
```

**Step 3: Verify only valid templates remain**

```bash
ls templates/
```

Expected: Only `404.json`, `article.json`, `blog.json`, `cart.json`, `collection.json`, `customers/`, `gift_card.liquid`, `index.json`, `list-collections.json`, `page.contact.json`, `page.json`, `page.story.json`, `page.wo-about.json`, `page.wo-radio.json`, `password.json`, `product.json`, `product.tee.json`, `search.json`.

**Step 4: Commit**

```bash
git commit -m "chore: remove legacy templates — page.about-world-order (replaced by page.wo-about.json), page.canvas.json (wrong section type)"
```

---

## Task 5: Merge to Main and Push

**Step 1: Check the branch log looks clean**

```bash
cd "/Users/jordansimo/Documents/Claude/Projects/world order/worldorder-repo"
git log --oneline main..fix/site-audit-issues
```

Expected: 4–6 commits listed (the rebase chain + our new commits). Verify no "Update from Shopify" commits appear that shouldn't be there.

**Step 2: Checkout main**

```bash
git checkout main
```

**Step 3: Merge the feature branch**

```bash
git merge fix/site-audit-issues --no-ff -m "merge: sync live site, catalog/canvas fixes, dead code cleanup"
```

**Step 4: Verify the merge**

```bash
git log --oneline -8
git status
```

Expected: clean working tree, `main` is ahead of `origin/main`.

**Step 5: Push both branches**

```bash
git push origin main
git push origin fix/site-audit-issues
```

Expected: Both pushes succeed. If `main` is rejected (remote has diverged), check with Jordan before doing `--force-with-lease`.

---

## Verification Checklist

After all tasks complete:

- [ ] `git status` shows clean working tree on `main`
- [ ] `git log --oneline -5` shows the merge commit at HEAD
- [ ] `diff -rq worldorder-v2-live-patched/ worldorder-repo/` shows no unexpected diffs (only `.git`, `docs/`, `.gitignore` are expected)
- [ ] `ls sections/wo-*.liquid` does NOT include `wo-editorial-strip`, `wo-footer`, `wo-manifesto-band`
- [ ] `ls templates/` does NOT include `page.about-world-order.liquid` or `page.canvas.json`
- [ ] GitHub reflects the push: https://github.com/studentdiscount-hub/worldorder-theme
