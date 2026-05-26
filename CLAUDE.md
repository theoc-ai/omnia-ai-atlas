# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**OMNIA: General Insurance AI Maturity Assessment** — an interactive single-page web app that helps insurance professionals self-assess AI maturity across their value chain and explore real-world AI case studies from major carriers (Progressive, AXA, Allianz, Liberty Mutual, etc.).

Deployed at: https://omnia-ai-atlas-gi.vercel.app

## Development

No build toolchain. The entire app is a single `index.html` file with all HTML, CSS, and JavaScript inline. To preview locally:

```bash
# Python
python -m http.server 8080

# Node (if npx available)
npx serve .
```

Deploy by pushing to `main` — Vercel auto-deploys.

## Architecture

### Single-file SPA
Everything lives in `index.html`: data definitions, styles, and all application logic. There is no bundler, framework, or external JS dependencies — the only external resources are the Manrope font (Google Fonts) and inline SVGs.

### Dual-mode UI
The app has two primary modes controlled by toggling the `mode-assess` class on `<body>`:
- **Assess mode**: Dual 0–5 sliders per subdomain to rate current state vs. target; sticky progress bar tracks completion toward report generation
- **Explore mode**: Browse/expand/filter case studies by domain

Mode preference persists in `localStorage`.

### Data structure
Insurance capabilities are organised as a two-level hierarchy embedded directly in the JS:
- **Tier 1 — 8 domains**: 5 Core Business (Product & Distribution, Quoting & Underwriting, Policy Servicing, Claims, Risk/Capital/Investment) + 3 Enabling Functions (Finance, HR, IT)
- **Tier 2 — ~27 subdomains**: each containing granular activities
- Each activity can embed one or more **case studies** with context, outcomes, and source citations

### State & persistence
All mutable state is stored in `localStorage`:
- Assessment slider ratings (current state + target per subdomain)
- Shortlisted items (activities/case studies bookmarked by the user)
- Active mode (assess vs. explore)

No server-side state; the app is fully client-side.

### Event handling
The app uses **delegated event handling** — a single click handler is attached to the top-level grid container and routes interactions based on the target element's data attributes or class names (expand/collapse domains, open case-study modals, toggle shortlist items, etc.).

### Lead capture
A modal form collects name, email, organisation, and role. Submission triggers personalised report generation mapping the user's capability gaps to relevant case studies. No backend is wired up in the static file — any integration point will be in the form submit handler.

## Design System

| Token | Value |
|---|---|
| Background primary | `#014348` |
| Background secondary | `#002f33` |
| Accent / CTA | `#C1EFA5` (green) |
| Font family | Manrope, 200–800 weight |
| Font size range | 11px labels → 38px headings |

## Deployment Config

`vercel.json` applies security headers globally (no routing rules):
- `X-Content-Type-Options: nosniff`
- `Referrer-Policy: strict-origin-when-cross-origin`
- `Permissions-Policy`: geolocation, microphone, camera all disabled
