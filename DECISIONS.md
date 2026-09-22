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
- **Custom domain set on GitHub Pages**: `cname=drabiancarubi.com`, CNAME file committed. Waiting on Namecheap records (A @ → 185.199.108–111.153, CNAME www → drabiancarubi.github.io). Enforce HTTPS after cert issuance.

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
