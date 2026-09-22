# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Professional website for **Dra. Bianca Rubí Flores Reyes**, anesthesiologist in Baja California Sur, Mexico. B2B referral site — the audience is *other doctors* (surgeons, dental surgeons, imaging units) who book her anesthesia services. Every primary CTA links to her cal.com booking page. See `DECISIONS.md` for the full decision log (palette, fonts, DNS plan, content sources) — **read it before making design or content changes, and append to it when making new decisions.**

## Stack & structure

Pure static HTML/CSS/JS — no framework, no build step, nothing to install. Hosted on GitHub Pages from `main` root at `drabiancarubi/website`.

- `index.html` — Spanish (primary, es-MX)
- `en/index.html` — English mirror (same structure/IDs, translated text; keep the two in sync when editing content)
- `assets/css/style.css` — entire design system (CSS custom properties at top define the palette)
- `assets/js/main.js` — nav toggle, scroll reveal, year
- `DECISIONS.md` — decision log; append, don't rewrite history

## Commands

- Preview locally: `python3 -m http.server 8000` then open http://localhost:8000
- Deploy: just `git push` (Pages serves `main` directly, live in ~1 min)

## Hard rules

- **All internal paths must be relative** (no leading `/`) — the site is served under the `/website/` subpath until the custom domain `drabiancarubi.com` (Namecheap, currently parked) is connected.
- Spanish is the source of truth for copy; mirror changes into `en/`.
- Section IDs are Spanish on both pages — never translate IDs (shared CSS/JS/anchors).
- The booking URL `https://cal.com/bianca-rubi-flores-reyes-wnescc/consulta-pre-anestesica` must remain on every CTA.
- Pushing requires the **drabiancarubi** gh account to be active: `gh auth switch -u drabiancarubi`.
