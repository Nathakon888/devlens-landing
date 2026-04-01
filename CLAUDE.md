# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

DevLens is a B2B SaaS "Developer Enrichment API" landing page. It accepts a GitHub username and returns a structured technical profile (language stack, seniority level, OSS tier, specializations, recruiter signals). This is a **static HTML site** with no build framework.

## Development

No build process — just edit `index.html` and open it in a browser or serve it locally:

```bash
# Any local HTTP server works, e.g.:
python -m http.server 8080
# or
npx serve .
```

Deployment is automatic via Vercel on push to `main`.

## Architecture

Everything lives in a single file: **`index.html`** (~1,400 lines). It contains all CSS (in a `<style>` tag), all JavaScript (in a `<script>` tag), and all HTML. Sections are delimited by comments like `<!-- ═══ HERO ═══ -->`.

**Page sections (top to bottom):** Navbar → Hero → Problem comparison → Live Demo → Features → Pricing → FAQ → Footer

**`vercel.json`** rewrites `/success` and `/cancel` routes to `index.html`, where JavaScript detects the URL path and shows Stripe redirect banners.

## Backend & Integrations

**Backend API** (hosted on Railway): `https://web-production-e9518.up.railway.app`

| Endpoint | Purpose |
|---|---|
| `GET /v1/enrich/{username}` | GitHub enrichment data |
| `POST /billing/checkout?{plan}` | Initiates Stripe checkout |
| `POST /billing/portal` | Opens customer subscription portal |
| `POST /register/free` | Registers free-tier user, emails API key |

**Stripe**: Handles Growth/Team tier payments. After checkout, Stripe redirects to `/success` or `/cancel`.

**Demo API key** (`dl_4ee5237be84955180f6114fce17643b1472e32957f6c03cd`) is hardcoded intentionally — it's a public, rate-limited demo key for the live demo widget.

## Key JavaScript Behaviors

- **Live demo widget**: Fetches `/v1/enrich/{username}` using the demo key; renders result with `escapeHtml()` for XSS safety
- **Pricing flow**: Collects email → calls backend → redirects to Stripe (paid) or confirms registration (free)
- **Success/cancel banners**: Created dynamically when `window.location.pathname` is `/success` or `/cancel`; auto-dismiss after 8 seconds
- **Scroll animations**: IntersectionObserver-based `fadeUp` with 5 staggered delay classes

## Design System (CSS Variables)

```
--bg-primary:   #06060b   (near-black background)
--accent:       #00dfa8   (cyan-green primary accent)
--accent-blue:  #00b8ff   (blue secondary accent)
--danger:       #ff4466   (red / cancel)
--text:         #e8e8f0   (off-white body text)
```

Fonts: **Syne** (headers), **DM Sans** (body), **Fira Code** (mono/code blocks) — loaded from Google Fonts.
