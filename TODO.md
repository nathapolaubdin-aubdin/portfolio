# TODO — Deploy Readiness & Language Toggle

_Last updated: 2026-07-18_

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
- [x] Deleted stray root file `Screenshot 2026-07-16 235144.png` (old layout-debug screenshot, unreferenced)
- [x] Added `.gitignore` (excludes `.venv/`, `__pycache__/`, OS junk files)
- [x] `git init` + first commit done (local identity: Nathapol Aubdin / nathapolaubdin@gmail.com, repo-scoped only)

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

- [x] Resume download button added to all 16 navbars (between the language toggle and Contact), styled as a bordered pill matching `.lang-toggle`; opens `Resume/Nathapol-Aubdin-Resume.pdf` in a new tab (`target="_blank"`, not a forced download)
  - **Still a placeholder file at `Resume/Nathapol-Aubdin-Resume-test.pdf`** — leftover from testing, not linked anywhere. Nathapol asked not to touch the Resume folder for now; revisit before final deploy (delete the test file, confirm the real PDF is final).
- [x] Grouped `.lang-toggle` + Resume + Contact into a `.nav-actions` wrapper with a tighter gap than the page-nav links; on mobile (`max-width:760px`) this wrapper stacks vertically so it doesn't get clipped in the slide-out menu
- [x] View Transitions enabled (`@view-transition{navigation:auto}` in `style.css`) for smoother crossfades between pages (Chromium + Safari 18+; no-op elsewhere)
- [x] Active-page nav underline (`.nav-active-dot`) now animates/slides between pages via `view-transition-name` — only wired on the 4 pages that have a current-page indicator: `index.html`, `UXUI.dc.html`, `Graphics.dc.html`, `About.dc.html` (`Contact.dc.html` and the `Work-*.dc.html` pages never had this indicator)

## Research notes

- **No build step needed** — `support.js` loads React/ReactDOM/Babel from `unpkg.com` at runtime. Any static host works.
- **`.dc.html` naming doesn't matter functionally** — the `<x-dc>` parser triggers off the tag, not the filename.
- **No `dc-import`/`x-import` usage anywhere** — nothing depends on the `.dc.html` extension.
- Site uses only relative paths — portable to any static host/subpath.
- **Runtime quirk to remember:** this platform's `support.js` injects `html,body{height:100%}` in non-preview mode, which makes `<body>` (not `window`/`<html>`) the actual scrolling element. Any future scroll-position JS must read `document.body.scrollTop` first, not `window.scrollY`.

## Still to do

- [ ] **Resume folder cleanup** — delete the leftover `Resume/Nathapol-Aubdin-Resume-test.pdf` placeholder once the real resume is finalized (Nathapol asked to hold off on touching PDFs for now)
- [ ] **Case-sensitivity audit** — dev was on Windows (case-insensitive); most hosts are case-sensitive Linux. Click through every link on the live URL after deploy.
- [ ] **Test filenames with spaces** render correctly after deploy (spot-check)
- [ ] **Choose hosting provider** — decided on git-based auto-deploy (GitHub Pages/Netlify/Vercel/Cloudflare Pages); specific provider not yet chosen
- [ ] **Push to GitHub + connect hosting** — repo is initialized locally with one commit, not yet pushed anywhere
- [ ] Nice-to-have: meta description / Open Graph tags, `robots.txt`, `sitemap.xml`

## Open questions for next session

- Which specific git-based host (GitHub Pages vs Netlify vs Vercel vs Cloudflare Pages)?
- Custom domain: decided to skip for now, using the host's free subdomain — revisit later if wanted
