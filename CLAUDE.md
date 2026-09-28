# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Professional website for **Dra. Bianca Rubí Flores Reyes**, anesthesiologist in Baja California Sur, Mexico — **live at https://drabiancarubi.com** (GitHub Pages, custom domain, HTTPS enforced). B2B referral site: the audience is *other doctors* (surgeons, dental surgeons, imaging units) who book her anesthesia services. The conversion goal is a booking through the **Cal.com inline calendar** embedded in the `#agenda` section; nav/hero/contact CTAs scroll there.

See `DECISIONS.md` for the full decision log — **read its "Current state" section before making design or content changes, and append an entry when making new decisions.**

## Stack & structure

Pure static HTML/CSS/JS — no framework, no build step, nothing to install. Deployed by pushing to `main` (`drabiancarubi/website`, public repo; Pages serves the root, live ~1 min after push).

- `index.html` — Spanish (primary, es-MX)
- `en/index.html` — English mirror (same structure and IDs, translated text; **keep the two in sync when editing content**)
- `assets/css/style.css` — entire design system (palette tokens at top)
- `assets/js/main.js` — nav toggle, scroll reveal, year, language-choice memory
- `assets/img/og-card.html` — source of the link-preview card; after editing, re-render with the headless-Chrome command in DECISIONS.md
- `robots.txt`, `sitemap.xml` — bump `<lastmod>` in the sitemap on meaningful content changes
- `DECISIONS.md` — decision log; append, don't rewrite history

## Commands

- Preview locally: `python3 -m http.server 8000` → http://localhost:8000
- Deploy: `git push` (requires the **drabiancarubi** gh account active: `gh auth switch -u drabiancarubi`)
- Verify mobile layout with real device emulation (CDP `Emulation.setDeviceMetricsOverride`), NOT bare `--window-size` screenshots — those misrender (see DECISIONS.md)

## Hard rules

- **All internal paths must stay relative** (no leading `/`).
- Spanish is the source of truth for copy; mirror every change into `en/`. Section IDs are Spanish on both pages — never translate IDs.
- The FAQ accordion (`#preguntas`) and its `FAQPage` JSON-LD must stay in sync — Google penalizes markup that doesn't match visible content. Same for the `Physician` JSON-LD in both heads.
- Booking URL `https://cal.com/bianca-rubi-flores-reyes-wnescc/consulta-pre-anestesica` powers the inline embed and the fallback links — never remove the fallback link under the embed.
- The Cal embed is lazy-loaded (IntersectionObserver in the inline script at the bottom of both pages); don't convert it to eager loading.
- Auto language detection: inline script in each page's `<head>`; a manual ES/EN click stores `localStorage.lang` which always wins. Don't break the `data-setlang` attributes.
- Photo work: use the rembg (isnet-general-use) pipeline documented in DECISIONS.md — never color-threshold extraction (it eats the white coat). Keep hero `<img>` width/height attrs matching the actual exported file.
