# NorthPeak Digital — one-page site

A responsive one-page marketing site for the fictional agency **NorthPeak Digital**.
Built with plain HTML, CSS and JavaScript — no page builder, no framework, no build step.

## Structure

```
northpeak-digital/
├── index.html   # markup for all sections
├── style.css    # design tokens, layout, responsive rules
├── script.js    # mobile nav + contact form validation
└── README.md
```

## Sections

- **Hero** — headline, subhead, primary CTA, three quick stats
- **Services** — grid of 6 disciplines (brand, product design, dev, growth, content/SEO, analytics)
- **Results** — three headline metrics + three testimonial cards
- **Pricing** — three tiers (Basecamp / Ascent / Summit), middle tier highlighted
- **Contact** — form with client-side validation (name, email, budget, message) and inline error messages; company field is optional

## Responsive behavior

Tested at 360px, 768px and 1440px:
- **360px** — single column throughout, hamburger nav, stacked stats/cards
- **768px** — 2-column services grid, stacked pricing (featured tier floats to top), collapsed nav
- **1440px** — full 3-column grids, sticky nav bar, side-by-side contact layout

No dependencies beyond two Google Fonts loaded via `<link>` in `index.html` (Fraunces, Inter, JetBrains Mono). The site works fine without them too — it falls back to system serif/sans-serif.

## Running locally

No build step. Either:
- Open `index.html` directly in a browser, or
- Serve it locally: `npx serve .` or `python3 -m http.server` from this folder, then visit `http://localhost:8000`

## Deploying

This repo is deploy-ready for any static host. Three options:

### Netlify (drag-and-drop, fastest)
1. Go to [app.netlify.com/drop](https://app.netlify.com/drop)
2. Drag the whole `northpeak-digital` folder onto the page
3. Netlify gives you a live URL immediately (e.g. `random-name.netlify.app`)
4. Optional: `Site settings → Change site name` for a cleaner URL

### Netlify (via Git, for continuous deploy)
1. Push this folder to a new GitHub repo (see below)
2. In Netlify: `Add new site → Import an existing project → GitHub` → pick the repo
3. Build command: leave blank · Publish directory: `/` (or wherever `index.html` lives)
4. Deploy — every future push to `main` auto-deploys

### Vercel
1. Push this folder to a GitHub repo
2. [vercel.com/new](https://vercel.com/new) → import the repo
3. Framework preset: **Other** (static) · Build command: none · Output directory: `/`
4. Deploy

### GitHub Pages
1. Push this folder to a GitHub repo (see below)
2. On GitHub: `Settings → Pages → Source → Deploy from a branch → main → / (root)`
3. Save — the site publishes at `https://<username>.github.io/<repo-name>/`

### Pushing to a public GitHub repo

From inside this folder:

```bash
git init
git add .
git commit -m "NorthPeak Digital — one-page site"
git branch -M main
git remote add origin https://github.com/<your-username>/northpeak-digital.git
git push -u origin main
```

Then make the repo public under `Settings → General → Danger Zone → Change visibility` if it wasn't created public already.

## Notes

- The contact form validates client-side only — there's no backend, so "sending" is simulated with a short delay. Wire it to a real endpoint (Netlify Forms, Formspree, your own API) by replacing the `setTimeout` block in `script.js` with an actual `fetch()` call. Netlify Forms is the fastest option: add `data-netlify="true"` and a hidden `form-name` input to the `<form>` tag and Netlify will handle submissions automatically once deployed there.
- All copy, client names, and metrics are fictional, written for this demo.
