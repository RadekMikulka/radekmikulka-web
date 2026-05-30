# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Static export of **radekmikulka.cz** — a personal marketing consultant portfolio. Two pages:
- `/` → `index.html`
- `/videa` → `videa/index.html`

Deployed to Vercel, connected to GitHub (`main` branch → auto-deploy).

## Deploy

```bash
git add -A && git commit -m "..." && git push   # triggers Vercel auto-deploy
vercel --scope radekmikulkas-projects            # manual deploy if needed
```

No build step. Vercel serves static files directly.

## How pages were created

Pages are a **static crawl of a WordPress/Elementor site**, not hand-written HTML. They are self-contained — all CSS, JS, fonts and images live in `wp-content/` and `wp-includes/` and are committed to the repo.

Key things to know when editing HTML:
- **Elementor CSS** lives in `wp-content/uploads/elementor/css/post-37.css` (homepage) and `post-897.css` (videa). Background images for sections are defined there, not in the HTML.
- **`elementor-invisible`** was removed from all elements — do not re-add it (it permanently hides content without Elementor JS).
- **YouTube videos** on `/videa` are plain `<iframe>` tags injected into `.elementor-video` divs. Elementor's `data-settings` JSON is present but ignored.
- **File names** must not contain `?` — Vercel treats `?` as a query string separator. Assets are named without version suffixes (e.g. `frontend.min.css`, not `frontend.min.css?ver=3.35.8`).

## Adding a new page

1. Create `page-slug/index.html`
2. Add a rewrite in `vercel.json` if `cleanUrls` doesn't pick it up:
   ```json
   { "source": "/page-slug", "destination": "/page-slug/index.html" }
   ```
3. Add canonical and og:url pointing to `https://www.radekmikulka.cz/page-slug`
4. Update navigation links in `index.html` and `videa/index.html`

## SEO tags (must be set on every page)

```html
<title>Page Title | Radek Mikulka</title>
<meta name="description" content="..." />
<link rel="canonical" href="https://www.radekmikulka.cz/page-slug" />
<meta property="og:title" content="..." />
<meta property="og:description" content="..." />
<meta property="og:image" content="https://www.radekmikulka.cz/wp-content/uploads/2024/02/rm-og-image.jpg" />
<meta property="og:url" content="https://www.radekmikulka.cz/page-slug" />
<meta property="og:type" content="website" />
```

og:image must be an **absolute URL** for social sharing to work.

## robots.txt

Contains a stale sitemap URL (`http://radekmikulka.cz/sitemap27.xml`). Update or replace when a sitemap is created for the static site.

## Vercel config notes

- `cleanUrls: true` — serves `page/index.html` at `/page`
- `trailingSlash: false` — no trailing slashes
- `wp-content/**` assets get `Cache-Control: immutable, max-age=31536000`
- Custom domains: `www.radekmikulka.cz` and `radekmikulka.cz` (both added via Vercel CLI)
