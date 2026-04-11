# Canvas / Hero / Footer Design

**Goal:** Elevate three core site surfaces to match the brand's confrontational, typographic identity — Canvas page (Radio editorial treatment), homepage hero copy, and footer.

**Decisions:**
- Canvas: editorial printed-insert treatment, no Spotify embed, pure type on dark background
- Hero: two-line tension structure — provocation + declarative landing
- Footer: minimal — manifesto fragment, Instagram, policy links only

---

## Canvas Page

**Layout:** Full-width, max-width 680px centered column, dark background (#1A1A18).

**Structure (top to bottom):**
1. EP label — "WORLD ORDER RADIO" — IBM Plex Mono, 10px, 0.25em tracking, uppercase, burgundy (#5F1804)
2. EP title — "There's a shape forming in the dark" — IBM Plex Mono Bold, large (clamp 20px→28px)
3. Tagline — "The clothes arrived first. The music explains why." — italic, 15px, 65% opacity
4. Tracklist — hairline top border, then each track: roman numeral (burgundy), track name (uppercase mono), artist (right-aligned, 50% opacity), hairline bottom border
5. CTA — "Listen on Spotify →" — IBM Plex Mono, 10px, 0.2em tracking, uppercase, 40% opacity

**Implementation:** Template JSON settings update only — no new Liquid. The `wo-radio-content` section already supports all these fields. The visual treatment upgrade (font sizing, spacing, dark bg) is a CSS pass on the existing section styles.

---

## Homepage Hero

**Copy (approved):**
> The world runs on a managed lie.
> This is the uniform.

**Layout:** Two lines, vertically centered on the hero area. No subhead. No CTA. IBM Plex Mono or Big Shoulders Display. The existing `wo-about-strip` section below handles the brand description — that copy gets stripped down to match the colder register, or removed entirely in favor of leading directly into the product strip.

**Implementation:** Update `index.json` — `wo-hero` section `headline` field gets the two lines. The `wo-about-strip` text gets updated or the section is removed from the template order.

---

## Footer

**Structure:**
- Top row: `© 2026 world order` (left) — `never. numb. again.` in IBM Plex Mono, burgundy (center) — `Instagram →` (right)
- Bottom row: policy links — Privacy · Refund · Terms · Shipping · Contact — IBM Plex Mono, min size, 35% opacity

**Implementation:** Replace the Dawn `footer.liquid` content block with a custom `wo-footer` section. The existing footer.liquid is a generic Dawn block — swapping it for a purpose-built minimal section gives full control without fighting the theme's block system.

---

## Constraints

- No new Liquid section files for Canvas or Hero — settings-only changes
- Footer requires one new section file: `sections/wo-footer.liquid`
- All special characters via HTML entities (not shell heredocs) per theme encoding constraint
- All inline CSS — no external stylesheet files (CDN 404 risk)
- Branch: `fix/site-polish` branched from `fix/critical-bugs`
