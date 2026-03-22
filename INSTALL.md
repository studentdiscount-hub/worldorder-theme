# WORLD ORDER — Shopify Implementation Guide
## Theme: "Updated copy of Updated copy of Main" (ID: 148710555839)

---

## FILES IN THIS PACKAGE

```
worldorder-shopify/
├── assets/
│   └── wo-theme.css              ← Master stylesheet (upload once)
├── sections/
│   ├── wo-hero.liquid            ← Homepage hero
│   ├── wo-editorial-strip.liquid ← 3-pillar editorial row
│   └── wo-manifesto-band.liquid  ← Dark manifesto + radio teaser
├── templates/
│   ├── index.json                ← Homepage section order
│   ├── page.about.liquid         ← About page template
│   └── page.radio.liquid         ← Radio/Canvas page template
└── INSTALL.md                    ← This file
```

---

## INSTALLATION ORDER

### Step 1 — Upload the CSS
1. Go to: Admin > Online Store > Themes > ··· > Edit code
2. Under **Assets**, click **Add a new asset**
3. Upload `wo-theme.css`

### Step 2 — Add the Sections
1. Still in the code editor, under **Sections**, click **Add a new section**
2. For each file in `/sections/`, create a new section with the same filename:
   - `wo-hero.liquid`
   - `wo-editorial-strip.liquid`
   - `wo-manifesto-band.liquid`
3. Paste the contents of each file

### Step 3 — Add Page Templates
1. Under **Templates**, click **Add a new template**
2. Choose **page** as the type
3. Create `page.about` and paste `page.about.liquid`
4. Create `page.radio` and paste `page.radio.liquid`

### Step 4 — Assign Templates to Pages
1. Go to: Admin > Online Store > Pages
2. Open **About** → change Template to `page.about`
3. Open **Canvas** (or create a new page called "Radio") → change Template to `page.radio`

### Step 5 — Build the Homepage
1. Go to: Admin > Online Store > Themes > Customize
2. On the homepage, remove existing sections
3. Add sections in this order:
   - **WO — Hero**
   - **WO — Editorial Strip** (add 3 Pillar blocks)
   - **WO — Manifesto Band**

---

## PRODUCT DESCRIPTIONS
Copy-paste these directly into each product's Description field in Admin > Products.

### 001 — annapolis: origin tee (black + white)
```
Named for the city, not the image of it. 1649 printed front, the founding 
seal redrawn without ceremony. Wear it if you know what the bay costs.
```

### 002 — axis t-shirt
```
The brand's point of origin on cotton. Three arrows, a single vertical axis. 
Direction without destination. This is the first uniform.
```

### 003 — fake i.d. hoodie
```
Icarus inside a caution triangle. Hazard symbol for the overachiever. 
The sleeve reads: THIS WILL NEVER OUTRUN ME. It will try anyway.
```

### 004 — campaign sticker
```
The mark without the garment. Put it somewhere permanent. Drop it on a 
laptop, a notebook, a wall you're about to leave behind.
```

---

## NAVIGATION UPDATE
In Admin > Online Store > Navigation > Main menu:
- Rename "catalog" → keep as "catalog"
- Rename "canvas" → **radio** (point to /pages/radio or /pages/canvas)
- "about" → keep
- Consider adding: **shop** pointing to /collections/frontpage

---

## NOTES
- The CSS loads via `{{ 'wo-theme.css' | asset_url | stylesheet_tag }}` in each section — no global injection needed
- All font weights are loaded from Google Fonts in the CSS file
- Sections are built with the Shopify Section Schema so they're fully editable in the Theme Customizer
- The `index.json` template is optional — it's the cleanest way to set homepage section order without the customizer, but the customizer method works too
- To add a hero background image: Theme Customizer > WO Hero > image picker

---

## RECOMMENDED APPS (no-code additions)

| App | Purpose | Cost |
|-----|---------|------|
| **Klaviyo** | Email capture + drop announcement flows | Free tier |
| **Judge.me** | Reviews (off by default, activate at Drop 002) | Free |
| **Shopify Search & Discovery** | Filter by drop / collection | Free (built-in) |
| **Fontify** | Inject custom fonts globally without code | $8/mo |
| **Metafields Guru** | Add custom metafields (drop number, EP number) per product | Free |

---

*World Order — worldorderhq.com*
*Theme build v1.0 — March 2026*
