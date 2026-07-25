# Optimization changelog — NorthPeak Digital

A note on method before the list: I don't have a way to run actual Google
Lighthouse or take a screenshot of its report in this environment — this
sandbox has no internet access, so the Lighthouse CLI can't be installed and
Chrome can't reach a real audit backend. I'm not going to fabricate a
Lighthouse screenshot, since a made-up score would just be misinformation
with a nice UI. What I *can* do, and did do, is:

1. Make the real, verifiable changes below (you can diff them).
2. Prove their effect with tools I do have — WCAG contrast math, actual byte
   counts before/after minification, actual request counts.
3. Give you the exact 30-second steps to pull the real Lighthouse numbers
   yourself (see the bottom of this file) — the site is now static, minified,
   and dependency-free, so whatever score you get will reflect the code, not
   my sandbox's limitations.

---

## Accessibility fixes

All of these were found by computing actual WCAG 2 contrast ratios for
every text/background and icon/background pairing in the stylesheet
(formula: relative luminance → contrast ratio, same math Lighthouse and axe
use under the hood). Six pairings failed.

| Element | Before | Ratio | After | Ratio | What this buys |
|---|---|---|---|---|---|
| Eyebrow labels (`.eyebrow`) | sage `#7FA98A` on paper | **2.28:1** ❌ | sage-ink `#2F6047` on paper | 6.29:1 ✅ | Section labels are readable for low-vision users instead of failing WCAG AA (4.5:1 required for small text) |
| Hero highlight word ("summit") | accent-dark `#C9762F` on paper | 2.97:1 ❌ (fails even the 3:1 large-text minimum) | accent-ink `#8C531F` on paper | 4.2:1 ✅ | The one bit of hero copy meant to draw the eye is now actually legible, not just decorative |
| Service card icons | accent-dark `#C9762F` on paper | 2.97:1 ❌ | accent-ink `#8C531F` on paper | 5.38:1 ✅ | Non-text UI graphics need 3:1 minimum (WCAG 1.4.11) — icons were borderline invisible for anyone with mild contrast sensitivity |
| Pricing tier labels, stat captions, contact meta labels | sage `#7FA98A` on paper/paper‑alt | 2.28 / 2.09:1 ❌ | sage-ink `#2F6047` | 6.29 / 5.76:1 ✅ | Same fix applied everywhere the token was reused on a light background (I kept the brighter `--sage`/`--accent-dark` for the dark ink-background contexts, where they already passed) |
| Form error/success text | `#C0483F` / `#3F7A56` on paper | 4.28 / 4.39:1 ❌ (just under 4.5) | `#9E362F` / `#2A5D40` | 6.0 / 6.62:1 ✅ | Validation feedback — the most important text on the page during an actual form submission — now clears AA with margin, not by a hair |
| **Keyboard focus ring** | `outline: 2px solid var(--accent)` (`#E8934A`) on paper | **2.08:1** ❌ (needs 3:1) | `outline: 2px solid var(--ink)` on light sections, `var(--text-light)` on dark sections | 13.9:1 / 15.97:1 ✅ | This was the most important fix on the list. A focus indicator that fails contrast is functionally invisible to keyboard users — the exact people relying on it most |

Plus a few smaller, non-contrast a11y items:
- **Form fields now carry `required` and `aria-describedby="err-*"`**, so a screen reader announces both "required" and the specific validation message when a field fails, instead of relying purely on a visually-adjacent `<span>`.
- **Mobile nav toggle bumped from 40×40px to 44×44px** to meet the standard minimum touch-target size.
- **Placeholder text now uses `--text-mute` explicitly** instead of the browser default, which was inconsistent across browsers and sometimes low-contrast.
- **`type="button"` added to the nav toggle** so it can never be mistaken for a submit control.

Everything that was already solid stayed as-is: single `<h1>` → `<h2>` → `<h3>` hierarchy with no skipped levels, all inline SVGs marked `aria-hidden`, every form input already had a properly-associated `<label for>`, a working skip link, semantic landmarks (`header`/`main`/`nav[aria-label]`/`footer`), and `prefers-reduced-motion` respected.

## Performance fixes

| Change | Measured effect |
|---|---|
| Google Fonts stylesheet switched from a blocking `<link rel="stylesheet">` to `<link rel="preload" as="style" onload="…rel='stylesheet'">` with a `<noscript>` fallback | Removes the font CSS from the critical rendering path — the page can paint with fallback fonts immediately instead of waiting on a round trip to `fonts.googleapis.com` |
| Added `&display=swap` to the Google Fonts request | Text renders in a fallback font instantly and swaps in the webfont when it arrives, instead of staying invisible during the font fetch (avoids a flash of invisible text / blocked FCP) |
| **Dropped the unused JetBrains Mono 500 weight** from the font request | I grepped every `font-family: var(--font-mono)` rule in the stylesheet — weight 500 was never actually used anywhere, only 400. One fewer font file downloaded, for zero visual change. |
| `defer` added to `script.js` | Was already at the bottom of `<body>` so it wasn't blocking, but `defer` makes that explicit and future-proofs it if the tag ever moves |
| CSS minified: `style.css` → `style.min.css` | 17,680 bytes → 12,896 bytes (**‑27%**), gzip: 4,157 → 3,276 bytes |
| JS minified: `script.js` → `script.min.js` | 4,267 bytes → 3,886 bytes (**‑9%**, mostly comments — the file was already small and unminified logic doesn't compress as dramatically) |
| `index.html` now points at the `.min` files in production | Total page weight (HTML + CSS + JS, no fonts/images) is **~32.9 KB uncompressed**, and the page makes **4 requests total** — the document, one font-CSS request, one CSS file, one JS file. Zero images: every graphic on the page (logo mark, service icons, contour background) is inline SVG or CSS gradient, so there was no image-optimization work needed. |

Source files (`style.css`, `script.js`) are kept unminified in the repo for readability/editing — only `index.html` points at the `.min` versions for the actual deploy.

## Running Lighthouse yourself

Since I can't produce a real report from here, here's the fastest way to get one:

1. Open `index.html` in Chrome (locally, or once deployed — either works, it's a static site)
2. DevTools (`Cmd+Opt+I` / `F12`) → **Lighthouse** tab
3. Check **Performance** and **Accessibility**, device = your choice, click **Analyze page load**
4. Screenshot the summary circles — that's your Deliverable A

If either score comes back under 90, paste the report (or just the flagged audit names) back to me and I'll fix the specific thing it's pointing at.
