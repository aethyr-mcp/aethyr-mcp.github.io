# Aethyr brand integration: site handoff

This is the implementation brief for putting the Aethyr lockup and crystal mark into the site, replacing the current `◈ BlueprintReader` header mark. `STYLE_GUIDE.md` is the source of truth; everything here conforms to it (cyan-led palette, exact `:root` tokens, white wordmark, tight glow, void black). Where this doc and the separate Aethyr brand spec disagree, follow `STYLE_GUIDE.md`.

## Assets delivered

| File | Use | Notes |
|---|---|---|
| `aethyr-lockup.svg` | Primary header and hero signature (crystal, wordmark, particle weave) | Vector, scalable. Wordmark is locked outlines, not live type. Has a baked void backdrop (see Background). |
| `aethyr-crystal.svg` | Standalone mark: favicon, app icon, avatar, loading state, watermark | Square viewBox. Reads down to 26px. |
| `aethyr-lockup@2x.png` | Raster fallback for the lockup | 2240x760, on void black. |
| `aethyr-icon-512.png` | App and social icon, apple-touch-icon | 512x512, crystal on void black. |
| `aethyr-favicon-48.png`, `-32.png`, `-16.png` | Favicon PNG fallbacks | Crystal only. 24, 26, 64, 180 can be exported on request. |

Both SVGs already carry `role="img"`, a `<title>Aethyr</title>`, and a `<desc>`, so they expose an accessible name without extra markup.

## Logo system and placement

Use the right asset for the context (this mirrors `STYLE_GUIDE.md` section 4):

- Full lockup: the default. Header, hero, anywhere space allows. Minimum 160px wide.
- Crystal mark alone: square or tight contexts (favicon, app icon, avatar, loading, watermark). Minimum 26px.
- Clear space: keep a margin around the lockup equal to the height of one wordmark letter. Do not crowd it.

## Color tokens

The SVG fills are the `STYLE_GUIDE.md` tokens, hardcoded. They must stay in sync with `site/css/styles.css` `:root`. If a developer changes a token there, update the matching hex in the SVG, or inline the SVG and switch the brand fills to `var(...)` (see "Couple colors to tokens" below).

| Token | Hex | Where it appears in the art |
|---|---|---|
| `--void` | `#070710` | Backdrop rect, page field |
| `--cyan` | `#1ce6ff` | Crystal body and edges, primary glow, lead shards |
| `--magenta` | `#ff2bd6` | Refraction glints and a few typed shards only |
| `--violet` | `#9b6bff` | Refraction glints and weave shards only |
| `--ember` | `#ff8a4c` | A single refraction glint on the crystal |
| `--ink` | `#e8ecff` | Wordmark and white shards |

Magenta, violet, and ember appear only as refracted light, never as flat fills or UI accents. That discipline is what keeps the brand from going rainbow.

## Implementation

### 1. Place the files

Drop the assets into the site asset folder (for example `site/img/brand/`). Adjust the paths in the snippets below to match the real structure.

### 2. Header (replace the current mark)

Inline the SVG so it inherits crispness and can be themed and animated. Wrap it in the home link and give the link an accessible name.

```html
<a class="brand" href="/" aria-label="Aethyr, home">
  <!-- paste the contents of aethyr-lockup.svg here -->
  <svg class="brand-lockup" viewBox="0 0 1120 380" role="img" aria-label="Aethyr"> ... </svg>
</a>
```

```css
.brand-lockup {
  width: clamp(160px, 26vw, 320px); /* respects the 160px minimum */
  height: auto;
  display: block;
}
```

If inlining is inconvenient, reference the file instead. With this approach the baked void backdrop shows, so the header surface must be `--void` (see Background), or swap in a transparent variant.

```html
<a class="brand" href="/">
  <img src="/img/brand/aethyr-lockup.svg" alt="Aethyr" width="320" height="109">
</a>
```

### 3. Favicon and app icon

Put this in `<head>`. The SVG favicon is preferred; the PNGs are fallbacks for clients that do not take SVG.

```html
<link rel="icon" type="image/svg+xml" href="/img/brand/aethyr-crystal.svg">
<link rel="icon" type="image/png" sizes="48x48" href="/img/brand/aethyr-favicon-48.png">
<link rel="icon" type="image/png" sizes="32x32" href="/img/brand/aethyr-favicon-32.png">
<link rel="icon" type="image/png" sizes="16x16" href="/img/brand/aethyr-favicon-16.png">
<link rel="apple-touch-icon" href="/img/brand/aethyr-icon-512.png">
<meta name="theme-color" content="#070710">
```

### 4. Social and Open Graph

Point `og:image` and the Twitter card at the icon for now.

```html
<meta property="og:image" content="/img/brand/aethyr-icon-512.png">
<meta name="twitter:card" content="summary_large_image">
```

A dedicated 1200x630 card reads better in link previews than a square icon. That size is not in this set; request it and it can be produced from the lockup.

### 5. Background and the baked void backdrop

Both SVGs include a `fill="#070710"` rect so they match `--void`. On a `--void` page this blends seamlessly and needs nothing extra. On any other surface (a glass panel, a lighter section, an email) that rect shows as a black box. For those cases, use a transparent variant: delete the single line `<rect ... fill="#070710"/>` from the SVG. Transparent SVG and PNG versions can be provided prebuilt on request.

### 6. Couple colors to tokens (optional, recommended if inlined)

After inlining, replace the brand colors with CSS variables so the mark tracks `:root`. Map the few brand fills, and leave the bespoke facet tints alone:

- Crystal edge stroke `#1ce6ff` to `var(--cyan)`
- Center cut `#cdf6ff` to a light cyan, or keep as is
- Wordmark stroke `#eef1ff` to `var(--ink)`
- Refraction glints `#9b6bff`, `#ff2bd6`, `#ff8a4c` to `var(--violet)`, `var(--magenta)`, `var(--ember)`

### 7. Motion (optional)

Keep it atmospheric and seamless, and gate it behind reduced-motion per `STYLE_GUIDE.md`. A slow opacity breathe on the inlined lockup is enough; no snap-back.

```css
@media (prefers-reduced-motion: no-preference) {
  .brand-lockup { animation: aethyr-breathe 6s var(--ease, ease-in-out) infinite; }
}
@keyframes aethyr-breathe { 0%, 100% { opacity: .92 } 50% { opacity: 1 } }
```

## Accessibility

- The lockup carries `role="img"` and a `<title>Aethyr</title>`, so screen readers announce "Aethyr." The home link also has `aria-label`.
- The wordmark is artwork, not text. If the lockup serves as the page's main heading, add a visually hidden `<h1>Aethyr</h1>` for document structure and search, in addition to the SVG.
- Contrast is high (white wordmark and cyan crystal on void), so no color-only information is at risk.

## Do and Don't

Do:
- Place it on `--void` or near-black, with blacks kept deep.
- Keep it cyan-led; the crystal stays cyan.
- Keep the wordmark white, with no fill color.
- Keep the letterforms exactly as drawn, including the delta A and the open R. Do not re-set them in a font.
- Keep glows tight, not blurry haze.

Don't:
- Recolor the crystal.
- Add a fill color to the wordmark.
- Re-type the letters in a substitute font.
- Place it on a light or busy background.
- Let the glow gray the background or bleed to the frame edge.
- Add a drop shadow, outer stroke, or container box.
- Use any Epic or Unreal Engine marks, logos, or fonts. Unreal Engine may be named only as a compatibility statement, with the trademark attribution already in the footer.

## Repo rules this touches

- No em dashes or en dashes (U+2014, U+2013) anywhere in markup, copy, comments, or commits. Use commas, colons, periods, parentheses, a hyphen, or the word "to."
- Original art only. No third-party intellectual property in assets.
- Keep new assets consistent with the palette and motifs in `STYLE_GUIDE.md`.

## Open items available on request

- Transparent-background SVG and PNG set (no void rect).
- A 1200x630 social and Open Graph card.
- A wordmark-only locked SVG, for inline headers and footers where the crystal is redundant.
- The live header markup and CSS as a ready-to-drop component, wired to the tokens.
- A subtle seamless motion pass for the hero and a title-sequence direction.
