# Design & Build Decisions — drabiancarubi.com

Log of every meaningful decision so future work never depends on chat memory.
Newest entries at the bottom. Date format: YYYY-MM-DD.

## 2026-09-22 — Initial build

### Goal / audience
- Business goal: **surgeons, dental surgeons, and imaging units in Baja California Sur book Dra. Bianca Rubí for anesthesia services**. This is a B2B (doctor-to-doctor) referral site, not a patient-facing site.
- Secondary audience: English-speaking (US) doctors — hence the `/en/` version.
- Every primary CTA points to her cal.com booking page:
  `https://cal.com/bianca-rubi-flores-reyes-wnescc/consulta-pre-anestesica`

### Tech stack
- **Pure static HTML/CSS/JS, no framework, no build step.** Rationale: GitHub Free + GitHub Pages hosting, zero cost, zero maintenance, trivially editable. A build system adds nothing for a ~2-page brochure site.
- Hosting: GitHub Pages from `main` branch root of `drabiancarubi/website`.
  Initially at `https://drabiancarubi.github.io/website/`; custom domain `drabiancarubi.com` to be connected later (see DNS section).
- **All internal paths are relative** (no leading `/`) because the project-page URL lives under the `/website/` subpath until the custom domain is attached.

### Languages
- Spanish (es-MX) is primary at `/index.html`. English at `/en/index.html`.
- Two static pages instead of a JS language toggle: better SEO (Mexican doctors search in Spanish; crawlers index real HTML), `hreflang` alternates wired in both heads. Canonicals already point at `https://drabiancarubi.com/` (the future domain) intentionally.
- Section IDs stay in Spanish on both pages so anchors/CSS/JS are shared.

### Color palette
Extracted from the reference Framer site (https://elegant-lychee-668052.framer.app/), kept because it also matches her portrait (burgundy scrubs, pink lipstick):
| Token | Hex | Use |
|---|---|---|
| `--ink` | `#32135f` | deep violet — headings, dark bands, footer |
| `--berry` | `#b83270` | primary accent — CTAs (matches lipstick) |
| `--purple` | `#7126ff` | secondary accent — small highlights |
| `--lav-200/300` | `#e1dcfd` / `#d2c9f7` | lavender tints |
| `--lav-050` | `#faf8ff` | page background (added, not in source) |
| `--text` | `#3b2d55` | body text (added) |

Direction: feminine + professional + "female power" — berry/violet with airy lavender, high whitespace, soft shadows.

### Typography
- Display: **Fraunces** (Google Fonts) — elegant, slightly feminine serif with authority.
- Body: **Inter** — neutral, highly legible.
- Loaded via Google Fonts CDN with system-font fallbacks.

### Content
- All copy comes from `'/Users/jcordoba/Downloads/Dra. Bianca Rubi Página web.docx'` (client-provided). Sections: hero, formación, servicios (3 audience cards), trayectoria (timeline), filosofía (3 principles), agenda CTA, contacto.
- ⚠️ The docx says "Hospital Maria Luids de la Peña" — assumed typo, rendered as **"Hospital María Luisa de la Peña"**. **Verify with Dra. Rubí.**
- Phone/WhatsApp intentionally omitted — not provided. Contact = cal.com + email `drabiancarubi@gmail.com` + Instagram + TikTok.
- No patient-facing language; copy addresses colleagues ("Bienvenido(a), colega").

### Assets
- Portrait: background-removed PNG from client, resized to 1200px (`dra-bianca.png`) and 640px (`dra-bianca-sm.png`) via `sips`, used with `srcset`.
- Favicon: inline SVG monogram "BR" on berry→purple gradient (`assets/img/favicon.svg`).

### DNS (to do later)
Domain `drabiancarubi.com` is at **Namecheap** (currently parked). When connecting:
1. In repo settings → Pages → custom domain `drabiancarubi.com` (creates CNAME file).
2. At Namecheap: delete the parking CNAME (`www` → parkingpage.namecheap.com) and URL-redirect record; add
   - `A @` → 185.199.108.153 / 185.199.109.153 / 185.199.110.153 / 185.199.111.153
   - `CNAME www` → `drabiancarubi.github.io.`
3. Enable "Enforce HTTPS" once the cert is issued.
Keep the existing SPF TXT record (email forwarding).

### Repo visibility
- The repo was created private; **switched to public on 2026-09-22** because GitHub Pages on the Free plan requires a public repo. Pages enabled: `main` branch, root. Live at https://drabiancarubi.github.io/website/.
- `og:image` / JSON-LD image currently point at the github.io URL — **swap to https://drabiancarubi.com/... when the custom domain goes live.**

## 2026-09-22 — Photo removed / domain connection

- **Hero photo removed** at client request: the delivered "background removed" PNG had the checkerboard transparency pattern baked into the pixels (visible artifacts near the hair) and the framing cropped her head. Replaced with a `.hero-emblem` monogram panel so the two-column hero layout is unchanged. `og:image`/JSON-LD image tags removed until a new photo arrives.
- **Photo spec requested from client** (see chat): portrait 3:4, ≥1200×1600px, head fully in frame with headroom, original un-edited photo preferred (we do background removal ourselves), JPG/PNG/HEIC all fine.
- **Custom domain LIVE (2026-09-22)**: Namecheap records applied (4× A @ → 185.199.108–111.153, CNAME www → drabiancarubi.github.io), cert issued, `https_enforced=true`. Verified: apex+/en/ 200, www→apex 301, http→https 301, github.io→domain 301. og:image now points at drabiancarubi.com.

## 2026-09-22 — Automatic language detection

- Inline head script on `/` redirects first-time visitors whose browser reports no Spanish (`navigator.languages`) to `/en/` via `location.replace` (no history entry). The `/en/` page only redirects back if the stored preference is `es`.
- A manual click on the ES/EN switch (or footer language link, `data-setlang` attr) stores `localStorage.lang`, which **always wins** over auto-detection — no redirect loops, choice sticks across visits.
- Client-side redirect (not server/geo) because GitHub Pages has no server logic; browser language beats geolocation anyway (an American in Los Cabos still gets English).

## 2026-09-22 — Link-preview (share) card

- Designed a branded 1200×630 OG card (`assets/img/og-card.png`) — violet/berry gradient, BR monogram, name, specialty, coverage cities. Source kept at `assets/img/og-card.html`; re-render after edits with headless Chrome:
  `'/Applications/Google Chrome.app/Contents/MacOS/Google Chrome' --headless=new --screenshot="$PWD/assets/img/og-card.png" --window-size=1200,630 --hide-scrollbars --virtual-time-budget=6000 "file://$PWD/assets/img/og-card.html"`
- Full OG + Twitter `summary_large_image` tag sets on both pages (localized descriptions; `og:url` points at the future canonical domain).
- ⚠️ `og:image`/`twitter:image` currently use the github.io URL — **swap to https://drabiancarubi.com/assets/img/og-card.png when HTTPS on the custom domain is live** (same moment as enforcing HTTPS). Note: WhatsApp/Facebook cache previews aggressively; re-scrape via https://developers.facebook.com/tools/debug/ if a stale card shows.

## 2026-09-22 — Portrait salvage

- Pixel analysis of the client photo ('/Users/jcordoba/Desktop/PNG image 2 Background Removed.png'): real alpha channel on top, but the background-removal app **baked the checkerboard pattern into opaque pixels from row ~1128 down** (of 2560) — lower half unrecoverable (pattern blended into coat/hair colors).
- Salvaged the clean top zone as a **circular portrait medallion**: 1000px circle centered on the face at source coords (650, 620) (crop box 150,120→1150,1120), circular alpha mask (also excludes the laryngoscope at x>1150), exported 900×900 → `assets/img/dra-bianca-portrait.png/.webp`.
- Medallion replaces the BR monogram in `.hero-emblem` (`.hero-portrait` CSS: white ring, lavender radial backing). Also added to the OG share card (`og-card.html` → re-rendered `og-card.png`).
- The `.hero-emblem span` monogram CSS is retained as fallback if the photo is ever pulled again.
- A full-length replacement photo is still requested from the client (original camera file, portrait ≥1200×1600); when it arrives, consider returning to the full-height hero cutout.

### GitHub account note
Three `gh` accounts on this machine; **drabiancarubi must be the active account** for pushes (`gh auth switch -u drabiancarubi`). Its fine-grained PAT could not create repos via API; repo was created manually on github.com.

## 2026-09-22 — Photo v2: ML cutout, badges removed

- Client provided `'/Users/jcordoba/Desktop/PNG image 2.png'` — same shot, full body, checkerboard baked in as background (RGB, no alpha).
- Color-threshold flood-fill extraction **ate parts of the white coat** (flat bright coat pixels are indistinguishable from checker tiles by color) — client caught it. Lesson: never color-match white-on-white; use segmentation.
- Final pipeline: **rembg (isnet-general-use) with alpha matting** → targeted erase of a checker remnant in the bottom-left corner box (y>2050, x<220, flat gray mn≥125) → content-bbox crop → 1200w/640w WebP. Model cached at ~/.rembg. Coat, hair, and laryngoscope fully preserved.
- **Hero badge cards removed entirely** per client feedback (they covered her on mobile); the same credentials/coverage info lives in the trust strip below the hero. `.hero-badge` CSS retained but unused.
- Mobile overflow scare: screenshots via `--window-size=390` *without* mobile emulation misrender; with proper CDP mobile emulation both pages measure scrollWidth 390 = viewport, zero overflowing elements. Verify mobile with device emulation, not bare window-size.

## 2026-09-22 — Photo v3 (final): natural headshot

- Client provided `'/Users/jcordoba/Desktop/PNG image 5.png'` — a real photo (blurred wall background, no AI-smoothing, no checkerboard). Chosen over v2.
- Same pipeline: rembg (isnet-general-use) + alpha matting → bbox crop → 1200w/640w WebP (`dra-bianca.webp` 1200×2118). Background "cleaning" is achieved by full replacement with the hero gradient panel.
- Share-card medallion regenerated from this photo: square crop (0,250)→(1440,1690) of the cutout, circle mask; face taller than frame width, so the hair dome is trimmed (avatar convention) — never crop the chin.
- Hero `<img>` width/height attrs must match the real file (currently 1200×2118) — update them whenever the photo is re-exported.

## 2026-09-22 — Cal.com inline embed

- Inline booking calendar embedded in `#agenda` on both pages (replaces the plain CTA band). Client-provided embed snippet, namespace `consulta-pre-anestesica`, with `"theme":"light"` forced and `cssVarsPerTheme` brand colors: light `#b83270`, dark `#e64f96`.
- Nav "Agendar/Book", hero primary CTA, and contact-card button now scroll to `#agenda` (internal anchors); the direct cal.com URL remains as the fallback link under the embed and should stay somewhere on the page always.
- `.embed-card` wraps the widget (white card, min-height 560px to prevent layout jump); `useSlotsViewOnSmallScreen` keeps it usable on phones.
- Verified live: iframe loads (~570px tall), brand color visible on selected date.

## 2026-09-22 — SEO (on-site work done)

- **Titles/descriptions** rewritten city-first for local queries: ES "Anestesióloga en La Paz, Los Cabos y San José del Cabo | …", EN "Anesthesiologist in La Paz & Los Cabos, Mexico | …".
- **Structured data**: rich `Physician` JSON-LD on both pages (areaServed cities, availableService procedures, memberOf Consejo, alumniOf, ReserveAction → cal.com) + `FAQPage` JSON-LD backed by a *visible* FAQ accordion section (`#preguntas`, 6 questions targeting long-tail local queries). Google requires FAQ markup to match visible content — never add markup without the visible section.
- **Technical**: `robots.txt` + `sitemap.xml` (with hreflang alternates + x-default, also added to both heads), geo.region/geo.placename metas, hero image `<link rel="preload">`, Cal embed lazy-loaded via IntersectionObserver (rootMargin 900px, 8s fallback) for LCP.
- **Off-site actions pending (user)**: Google Search Console verification + sitemap submit; Google Business Profile (critical for Maps); Doctoralia profile; hospital/clinic website links; Instagram/TikTok bio links to the domain.
- When content changes meaningfully, bump `<lastmod>` in sitemap.xml.
