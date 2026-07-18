# TODO — Deploy Readiness & Language Toggle

_Last updated: 2026-07-19_

## 🟢 Live

**https://nathapolaubdin-aubdin.github.io/portfolio/** — deployed via GitHub Pages, auto-rebuilds on every push to `main`. Repo: `github.com/nathapolaubdin-aubdin/portfolio` (public — required for free GitHub Pages).

### Updating the site

- **Code changes:** `git add -A && git commit -m "..." && git push` from the project folder (git identity + GitHub auth already configured on this machine, no login needed). Pages rebuilds automatically within ~1-2 min of the push.
- **Resume PDF only:** no git needed — go to the repo on github.com → `Resume/` folder → "Add file" → "Upload files" → drag in the new PDF **named exactly `Nathapol-Aubdin-Resume.pdf`** → commit via the browser. Also triggers an auto-rebuild. (If continuing to work with Claude locally afterward, remember to `git pull` first so the local copy matches.)

### URL structure (clean URLs, no `.html`)

Every page except the homepage lives in its own folder as `index.html`, so GitHub Pages serves it at a trailing-slash URL with no filename — same mechanism that already makes `/portfolio/` serve the homepage without needing `/index.html`.

| Page | File path | Live URL |
|---|---|---|
| Home | `index.html` (root) | `/portfolio/` |
| About | `About/index.html` | `/portfolio/About/` |
| Contact | `Contact/index.html` | `/portfolio/Contact/` |
| Graphics | `Graphics/index.html` | `/portfolio/Graphics/` |
| UX/UI | `UXUI/index.html` | `/portfolio/UXUI/` |
| Case studies | `Work-<Name>/index.html` | `/portfolio/Work-<Name>/` (kept the `Work-` prefix per Nathapol's choice) |

Root `index.html` links to these with plain relative paths (`About/`, `Work-Prolog/`, etc). Every moved page links back with `../` (e.g. `../About/`, `../` for home, `../style.css` for shared assets) since it's now one directory deeper. **When adding a new page, follow this pattern** — don't add a new bare `Something.dc.html` at the root, create `Something/index.html` instead and use `../`-prefixed relative paths for its assets and nav links.

### Analytics

Google Analytics 4 (`gtag.js`, measurement ID `G-LJKXVH5P0V`) installed on all 16 pages, as high in `<head>` as possible per Google's recommendation. Verified locally that it loads and fires `page_view`/`scroll` events correctly. Netlify's own analytics was considered but is a paid add-on (~$9/mo), not free — went with GA4 instead since it's free on any host, no need to migrate off GitHub Pages for this. **When adding a future page, copy this same snippet into its `<head>` too** (right after `<head>` opens, before other tags).

## Done — Deploy readiness

- [x] Renamed `Portfolio.dc.html` → `index.html` (homepage entry point)
- [x] Updated all internal links across every page (49 occurrences across 16 files) to point to `index.html`
- [x] Updated `.vscode/launch.json` debug URL to match
- [x] Removed `Image_originals/` (57MB of unused raw source images, confirmed unreferenced)
- [x] Project size checked: largest tracked file is the resume PDF (~21.5MB) — under GitHub's 100MB hard limit, no Git LFS needed
- [x] Fixed horizontal scroll on mobile/tablet widths: added `overflow-x:hidden` to `html` in `style.css` (was only on `body`, which didn't reliably propagate in this runtime)
- [x] Fixed the homepage marquee and the scroll-progress bar/nav-blur-on-scroll being frozen on **every page**: the root cause was that `support.js`'s `FULL_PAGE_CSS` (`html,body{height:100%}`) makes `<body>` the real scrolling element in this runtime, but the per-page scroll-tracking code read `window.scrollY || document.documentElement.scrollTop` (always 0). Fixed by reading `document.body.scrollTop` first, in all 16 pages' inline scripts.
- [x] Fixed profile photo not staying a fixed aspect ratio across breakpoints (index.html home + About.dc.html): swapped a `height:clamp(...vh...)` container for `aspect-ratio:1/1` (the source photo is a 2380×2380 square)
- [x] Fixed the home page profile photo rendering oversized/overflowing on narrow screens: the `.about-grid` second column had `width:800px` (with `max-width:100%` as a weak safety net) — CSS Grid used the fixed `width` as the track's content-size floor, forcing the whole row to 800px wide regardless of viewport. Swapped to `width:100%;max-width:800px;`.
- [x] Fixed work-thumb case-study cards (home "Selected Work" + UX/UI project list) drifting/over-cropping during scroll: removed the inner `data-parallax` wrapper that oversized the image to allow scroll-linked movement — combined with `object-fit:contain` this created visible letterbox gaps that grew as the parallax shifted, worst on mobile where the frame's aspect ratio differs most from the source images. Images now sit directly in the `.work-thumb` frame at `object-fit:cover`, 100% fit, no drift. The reveal-on-scroll (`data-reveal`, whole card) and hover-zoom (`style-hover`, image only) are untouched.
- [x] Deleted stray root file `Screenshot 2026-07-16 235144.png` (old layout-debug screenshot, unreferenced)
- [x] Added `.gitignore` (excludes `.venv/`, `__pycache__/`, OS junk files)
- [x] `git init` + first commit (local identity: Nathapol Aubdin / nathapolaubdin@gmail.com, repo-scoped only)
- [x] GitHub CLI (`gh`) installed + authenticated on this machine (device-code web login, account `nathapolaubdin-aubdin`)
- [x] Repo created and pushed: `github.com/nathapolaubdin-aubdin/portfolio`
- [x] Repo switched public + GitHub Pages enabled (Settings → Pages → Deploy from branch `main` / root) — free tier requires public for Pages
- [x] Second commit pushed with all mobile-nav/scroll/parallax fixes below — site is live and current as of this update

## Done — EN/TH language toggle

**Status: all 16 pages fully bilingual**, including `About.dc.html` and `Contact.dc.html` (nav, hero, bio/contact copy, facts list labels+values, stats, experience timeline, footer CTA).

- [x] Infrastructure (shared across all pages):
  - `support.js` — custom addition (marked clearly since this file is otherwise auto-generated) that reads `localStorage["site-lang"]` (default `"en"`), sets `data-lang` on `<html>`, listens for `[data-lang-btn]` clicks via event delegation
  - `style.css` — `[data-lang-en]`/`[data-lang-th]` show/hide rules driven by `:root[data-lang]`, plus `.lang-toggle`/`.lang-btn` pill styling in the nav
- [x] EN/TH toggle button added to the navbar on all 16 pages (default language: EN)

### Key technical notes (for any future page/section)

- **Wrapper pattern:** `<span data-lang-en>...</span><span data-lang-th>...</span>` for short inline text (nav links, headings, single phrases). For **multi-paragraph body copy** (multiple `<p>` or `<li>`), use `<div data-lang-en>...</div><div data-lang-th>...</div>` instead — wrapping in `<span>` breaks block layout.
- **Load-bearing CSS fix:** the show/hide rule uses `display:contents` (not `display:inline`) for the active language, so the wrapper disappears from the box model while children (`<p>`, `<ul>`, `<span>`) keep their natural display. This is what makes the same pattern work for both single words and full paragraphs.
- **`sc-for`-driven data** (skills/knowledge/timeline/meta/facts arrays): add parallel `xxxEn`/`xxxTh` fields in the JS `renderVals()` object (e.g. `vEn`/`vTh`, `kEn`/`kTh` instead of a single `v`/`k`), then wrap each in the span pattern in the template.
- **Scope decisions (per Nathapol's instructions):**
  - Kept English in both languages: company/brand names, tool names, skill/category titles, methodology tags, job titles
  - Left as English-only "chrome" (not wrapped): small monospace all-caps UI decoration — section eyebrows like "(01) — SELECTED WORK", "SCROLL", "BACK TO TOP ↑", "BACK TO WORK/HOME", "© 2026 NATHAPOL AUBDIN", "AVAILABLE FOR CONTRACT", the marquee banner, stat number labels
  - Translated properly: headings, body paragraphs, card category tags, CTA button text, date ranges (Thai month abbreviations + Buddhist calendar), facts-list labels (Name/Role/Based in/etc.)

## Done — Nav additions

- [x] Resume link added to all 16 navbars (between the language toggle and Contact), styled as a bordered pill matching `.lang-toggle`; opens `Resume/Nathapol-Aubdin-Resume.pdf` in a new tab (`target="_blank"`, not a forced download — Nathapol wanted "view" not "download" behavior)
  - **Still a placeholder file at `Resume/Nathapol-Aubdin-Resume-test.pdf`** — leftover from testing, not linked anywhere. Revisit before final polish (delete the test file once the real resume is confirmed final). See "🟢 Live" section above for how Nathapol will update the real PDF himself (browser upload, no code changes needed).
- [x] Grouped `.lang-toggle` + Resume + Contact into a `.nav-actions` wrapper with a tighter gap than the page-nav links; on mobile (`max-width:760px`) this wrapper stacks vertically so it doesn't get clipped in the slide-out menu
- [x] On mobile, widened Resume button to 30px horizontal padding and Contact button to 40px (`style.css`, scoped to the `max-width:760px` media query via `a[href="..."]` attribute selectors — no HTML changes needed since both hrefs are unique per page)
- [x] View Transitions enabled (`@view-transition{navigation:auto}` in `style.css`) for smoother crossfades between pages (Chromium + Safari 18+; no-op elsewhere)
- [x] Active-page nav underline (`.nav-active-dot`) now animates/slides between pages via `view-transition-name` — only wired on the 4 pages that have a current-page indicator: `index.html`, `UXUI/index.html`, `Graphics/index.html`, `About/index.html` (`Contact/index.html` and the `Work-*/index.html` pages never had this indicator)
- [x] Migrated every page (except homepage) from `Name.dc.html` at the root to `Name/index.html`, so live URLs are clean (`/portfolio/About/` instead of `/portfolio/About.dc.html`) — see "URL structure" section above for the full mapping and the pattern to follow for future pages
- [x] Mobile nav drawer now closes on tap-outside, not just the X button: added a transparent full-screen `<label for="navToggle" class="nav-backdrop">` (pure CSS checkbox-hack technique, no JS) sitting behind the drawer (`z-index:99` vs drawer's `100`) that toggles the same checkbox. Deliberately **no scroll lock** while the drawer is open (Nathapol's call) — background can still scroll behind it.

## Research notes

- **No build step needed** — `support.js` loads React/ReactDOM/Babel from `unpkg.com` at runtime. Any static host works.
- **`.dc.html` naming doesn't matter functionally** — the `<x-dc>` parser triggers off the tag, not the filename.
- **No `dc-import`/`x-import` usage anywhere** — nothing depends on the `.dc.html` extension.
- Site uses only relative paths — portable to any static host/subpath.
- **Runtime quirk to remember:** this platform's `support.js` injects `html,body{height:100%}` in non-preview mode, which makes `<body>` (not `window`/`<html>`) the actual scrolling element. Any future scroll-position JS must read `document.body.scrollTop` first, not `window.scrollY`.
- **Checkbox-hack nav pattern:** the mobile drawer (`#navToggle` checkbox + `.navlinks` + `.nav-burger` label) is pure CSS, no JS. Adding a same-`for` `<label>` anywhere lets it also toggle the checkbox — used this for the tap-outside-to-close backdrop. Handy pattern if more click-to-toggle behavior is needed later without touching `support.js`.

## Still to do

- [ ] **Resume folder cleanup** — delete the leftover `Resume/Nathapol-Aubdin-Resume-test.pdf` placeholder once the real resume is finalized
- [ ] **Case-sensitivity audit** — dev was on Windows (case-insensitive); GitHub Pages serves from Linux (case-sensitive). Click through every link on the live URL to confirm nothing 404s.
- [ ] **Test filenames with spaces** render correctly on the live URL (spot-check — GitHub Pages generally handles this fine via URL-encoding, but worth confirming)
- [ ] Nice-to-have: meta description / Open Graph tags, `robots.txt`, `sitemap.xml`
- [ ] Nice-to-have: custom domain (skipped for now, using the free `github.io` subdomain — revisit if wanted later)

## Open questions for next session

- None blocking — site is live and functional. Remaining items above are polish/cleanup, not launch blockers.
