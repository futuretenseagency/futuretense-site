# Future Tense Agency — claude.md

This is the source of truth for all AI-assisted work on the Future Tense Agency website.
Read this before touching any file.

---

## Project Overview

**Site:** [futuretenseagency.com](https://futuretenseagency.com)
**Repo:** future_tense-website2.0
**Stack:** Pure static HTML/CSS/JS — no build step, no framework, no package.json
**Deployment:** Vercel (auto-deploys on push to main)
**Form backend:** Formspree (`https://formspree.io/f/xykbzrrp`)

---

## File Structure

```
/
├── index.html              # Main agency site
├── favicon.ico
├── FT_logo_stacked_transparent.png
├── robots.txt              # Root crawl rules (see Security section)
├── vercel.json             # Vercel headers config
├── .gitignore
├── tonchin/
│   └── index.html          # Client portal — Future Tense x Tonchin
└── loanmantra/
    ├── index.html          # Client portal — Future Tense x Loan Mantra
    └── robots.txt
```

**Do not create new top-level files without discussion.** New client portals go in their own subdirectory (e.g., `/clientname/index.html`).

---

## Deployment

- Push to `main` → Vercel auto-deploys. No build step required.
- There is no staging branch yet. Test locally before pushing.
- Do not modify `.vercel/project.json` — it's linked to the live Vercel project.
- `vercel.json` controls security headers for client subdirectories. Update it whenever a new client portal is added.

---

## Brand System

### Colors

| CSS Variable | Hex | Usage |
|---|---|---|
| `--black` | `#0A0A0A` | Default background |
| `--panel` | `#141414` | Secondary surfaces |
| `--card` | `#161616` | Card backgrounds |
| `--border` | `rgba(255,255,255,0.07)` | Subtle dividers |
| `--green` | `#2A8C00` | Primary accent, CTAs |
| `--glow` | `#36B800` | Hover states, glows |
| `--white` | `#FFFFFF` | Primary text |
| `--gray` | `#6B7280` | Secondary text |
| `--gray-light` | `#A0A4AB` | Tertiary text, captions |

**Color ratio: 60% black / 30% neutral / 10% green.**
Green is the accent. Never make it the dominant color.

Client portal pages use shorthand variable names (`--g`, `--blk`, etc.) — keep them consistent with the values above. Do not introduce new color values without updating this file.

### Typography

- **Headings/UI:** `'Space Grotesk', sans-serif` — weights 400, 500, 600, 700
- **Body:** `'DM Sans', sans-serif` — weights 300, 400, 500
- Both loaded via Google Fonts. Do not add additional font families.

### Voice

Sharp. Direct. No fluff. Forward-looking and confident.
- No corporate filler phrases
- No exclamation points in headlines
- Tagline: *"Human at the core. AI everywhere it counts."*

### Logo

Use `FT_logo_stacked_transparent.png` for dark backgrounds.
Never stretch, recolor, or place the green logo on a light background.

---

## SEO Rules

### Main site (index.html)

Every change to `index.html` must preserve or improve these:

- `<meta charset="UTF-8" />`
- `<meta name="viewport" content="width=device-width, initial-scale=1.0" />`
- `<meta name="description" content="..." />` — keep under 160 characters
- `<meta property="og:title" />`, `og:description`, `og:url`, `og:type`
- `<link rel="canonical" href="https://futuretenseagency.com" />` ← **add this if missing**
- `<meta name="twitter:card" content="summary_large_image" />` ← **add this if missing**

**Known issues to fix:**
- `og:image` is currently base64 inline — replace with a hosted URL (e.g., `/og-image.png`) so social previews render correctly
- No `sitemap.xml` exists yet — create one when the site has more than one indexable page
- No `404.html` exists — create a branded one

### Client portals (tonchin/, loanmantra/, and all future /clientname/ dirs)

Every client portal MUST have ALL of the following. No exceptions.

**In `<head>` of the HTML:**
```html
<meta name="robots" content="noindex, nofollow, noarchive, nosnippet, noimageindex">
<meta name="googlebot" content="noindex, nofollow">
```

**A `robots.txt` file in the subdirectory:**
```
User-agent: *
Disallow: /

User-agent: GPTBot
Disallow: /

User-agent: CCBot
Disallow: /

User-agent: anthropic-ai
Disallow: /

User-agent: PerplexityBot
Disallow: /

User-agent: Google-Extended
Disallow: /

User-agent: Bytespider
Disallow: /

User-agent: ClaudeBot
Disallow: /
```

**In `vercel.json`** — add a header block for the new path:
```json
{
  "source": "/clientname/(.*)",
  "headers": [
    { "key": "X-Robots-Tag", "value": "noindex, nofollow, noarchive, nosnippet, noimageindex" }
  ]
}
```

⚠️ **Current gap:** `tonchin/` is missing its own `robots.txt`. This must be created. The meta tags exist in the HTML but the file-level robots.txt is absent.

---

## Forms

The contact form in `index.html` submits to Formspree via `fetch()`:

```
action="https://formspree.io/f/xykbzrrp"
```

- Fields: `name` (required), `company` (optional), `email` (required), `message` (required)
- Submission is handled by inline JS — do not convert to a standard POST form
- Always test the form after any changes to the surrounding HTML
- Do not add new form endpoints without updating this file

---

## Client Portals

### Current portals

| Directory | Client | Status |
|---|---|---|
| `/tonchin/` | Tonchin | Pitch deck (static, no auth) |
| `/loanmantra/` | Loan Mantra | Pitch deck (static, no auth) |

### Rules for all client portals

1. Every portal is **noindex** — see SEO Rules above. Enforce at all three layers: HTML meta, subdirectory robots.txt, and vercel.json headers.
2. Portals are currently accessed by direct URL only (no auth). Do not add links to them from the main site.
3. All portals must include a "Back to Future Tense" link to `/`.
4. Base64 images embedded in portal HTML are intentional (keeps everything in one file). Do not externalize them unless file size becomes a real problem (>2MB).

### Adding a new client portal

1. Create `/clientname/` directory
2. Create `/clientname/index.html` using the existing portal pages as a template
3. Create `/clientname/robots.txt` using the template above
4. Add the `vercel.json` header block for `/clientname/(.*)`
5. Add the HTML meta noindex tags
6. Update the client portal table in this file

### Future: Auth system

The long-term plan is to add client login/auth to portals. When that work begins:
- Preferred approach: Supabase (auth + database, works with static frontend)
- Each portal will become a protected route
- Do not implement auth without a full architecture discussion first

---

## What to Clean Up (Known Tech Debt)

These exist in the repo and should be addressed:

| Item | Action |
|---|---|
| `install_favicon.py` | Delete — leftover script, no longer needed |
| `.DS_Store` | Add to `.gitignore` and remove from tracking |
| `og:image` base64 in index.html | Replace with hosted `/og-image.png` |
| Missing `tonchin/robots.txt` | Create immediately (security gap) |
| Missing `<link rel="canonical">` | Add to index.html |
| Missing `twitter:card` meta | Add to index.html |
| Missing `sitemap.xml` | Create when ready to optimize SEO |
| Missing `404.html` | Create branded error page |
| CSS variable naming inconsistency | Client portals use `--g`, `--blk` etc. — should align with main site naming in future refactor |

---

## What Claude Should Not Do Autonomously

- Do not change Formspree endpoint IDs
- Do not modify `.vercel/project.json`
- Do not add external JS libraries or CDNs without discussion
- Do not remove noindex meta tags from any client portal
- Do not link to client portals from the main site
- Do not create auth systems, databases, or backend services without architectural approval
- Do not commit `.DS_Store` files

---

## Architecture Principles

1. **Stay static as long as possible.** No build tools, no frameworks, no npm unless there is a clear reason.
2. **One file per page.** All CSS and JS stays inline or in `<style>`/`<script>` blocks. No separate asset directories yet.
3. **Security by default for client content.** Three-layer noindex enforcement (HTML + robots.txt + Vercel headers) on every non-public page.
4. **Brand consistency is non-negotiable.** Color values, fonts, and voice must match the brand system above on every page.
5. **This file is the authority.** If something contradicts claude.md, claude.md wins. Update this file when decisions change.
