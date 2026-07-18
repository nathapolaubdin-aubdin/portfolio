# TODO — Deploy Readiness & Language Toggle

_Last updated: 2026-07-18_

## Done — Deploy readiness

- [x] Renamed `Portfolio.dc.html` → `index.html` (homepage entry point)
- [x] Updated all internal links across every page (49 occurrences across 16 files) to point to `index.html`
- [x] Updated `.vscode/launch.json` debug URL to match
- [x] Removed `Image_originals/` (57MB of unused raw source images, confirmed unreferenced)
- [x] Project size checked: **104MB total**, largest file 17MB — under GitHub's limits, no Git LFS needed
- [x] Fixed a real pre-existing bug found during testing: `html{overflow-x:hidden}` in `style.css` broke scroll-propagation from `body`, disconnecting `window.scrollY` from real scrolling. This silently broke the homepage marquee AND the scroll-progress bar/parallax on **every page**. Removed the rule from `html` (kept on `body`) — one-line fix, benefits the whole site.

## Done — EN/TH language toggle

**Status: 15 of 17 pages fully bilingual.** Only `About.dc.html` and `Contact.dc.html` remain.

- [x] Infrastructure (shared across all pages):
  - `support.js` — custom addition (marked clearly since this file is otherwise auto-generated) that reads `localStorage["site-lang"]` (default `"en"`), sets `data-lang` on `<html>`, listens for `[data-lang-btn]` clicks via event delegation
  - `style.css` — `[data-lang-en]`/`[data-lang-th]` show/hide rules driven by `:root[data-lang]`, plus `.lang-toggle`/`.lang-btn` pill styling in the nav
- [x] EN/TH toggle button added to the navbar on all 16 pages (default language: EN)
- [x] Fully converted to bilingual:
  - `index.html` — nav, side-nav, hero, Selected Work, Graphics, Skills/Tools/Knowledge, About, Experience timeline (Buddhist calendar for TH), Contact
  - `UXUI.dc.html` — nav, hero, "All Projects" list (11 cards incl. date ranges), footer
  - `Graphics.dc.html` — nav, hero, footer (brand names like "Fender Audio Thailand" intentionally left as-is — proper nouns)
  - All **11** `Work-*.dc.html` case studies (Prolog, Rujoran, Asco, MatCanon, ThaiIOD, Pathumflex, Copel, Digimusketeers, DailyMu, MuCard, RealFactory) — nav, hero, meta row, full THE CHALLENGE/THE RESULT body copy, COLOR PALETTE/TYPOGRAPHY, footer
- [x] Extra homepage tweaks done along the way: PROJECTS stat 40+→15+, CLIENTS stat 15→10+, About section text column widened (new `.about-grid` class: photo fixed ~600px, text takes the rest)
- [x] Verified every converted page end-to-end in a real browser (local server + Chrome automation)

### Key technical notes (for finishing About/Contact, or any future page)

- **Wrapper pattern:** `<span data-lang-en>...</span><span data-lang-th>...</span>` for short inline text (nav links, headings, single phrases). For **multi-paragraph body copy** (multiple `<p>` or `<li>`), use `<div data-lang-en>...</div><div data-lang-th>...</div>` instead — wrapping in `<span>` breaks block layout.
- **Load-bearing CSS fix:** the show/hide rule uses `display:contents` (not `display:inline`) for the active language, so the wrapper disappears from the box model while children (`<p>`, `<ul>`, `<span>`) keep their natural display. This is what makes the same pattern work for both single words and full paragraphs.
- **`sc-for`-driven data** (skills/knowledge/timeline/meta arrays): add parallel `xxxEn`/`xxxTh` fields in the JS `renderVals()` object (e.g. `vEn`/`vTh` instead of a single `v`), then wrap each in the span pattern in the template.
- **Scope decisions (per Nathapol's instructions):**
  - Kept English in both languages: company/brand names, tool names, skill/category titles, methodology tags, job titles
  - Left as English-only "chrome" (not wrapped): small monospace all-caps UI decoration — section eyebrows like "(01) — SELECTED WORK", "SCROLL", "BACK TO TOP ↑", "© 2026 NATHAPOL AUBDIN", "AVAILABLE FOR CONTRACT", the marquee banner, stat labels (YEARS EXP, etc.)
  - Translated properly: headings, body paragraphs, card category tags, CTA button text, date ranges (Thai month abbreviations + Buddhist calendar)

## Research notes

- **No build step needed** — `support.js` loads React/ReactDOM/Babel from `unpkg.com` at runtime. Any static host works.
- **`.dc.html` naming doesn't matter functionally** — the `<x-dc>` parser triggers off the tag, not the filename.
- **No `dc-import`/`x-import` usage anywhere** — nothing depends on the `.dc.html` extension.
- Site uses only relative paths — portable to any static host/subpath.

## Still to do

- [ ] **Language toggle:** convert the last 2 pages — `About.dc.html` and `Contact.dc.html`
- [ ] **Not a git repo yet** — run `git init`, first commit
- [ ] **Add `.gitignore`** — exclude `.venv/` (14MB, unrelated) and decide on `Screenshot 2026-07-16 235144.png` (stray root file, unreferenced)
- [ ] **Case-sensitivity audit** — dev was on Windows (case-insensitive); most hosts are case-sensitive Linux. Click through every link on the live URL after deploy.
- [ ] **Test filenames with spaces** render correctly after deploy (spot-check)
- [ ] **Choose hosting + deploy** — git-based (GitHub Pages/Netlify/Vercel/Cloudflare Pages, auto-deploy on push) vs. no-git (Netlify Drop, Vercel CLI, FTP) — decision pending
- [ ] Nice-to-have: meta description / Open Graph tags, `robots.txt`, `sitemap.xml`

## Open questions for next session

- Which hosting option did we land on?
- Does Nathapol want a custom domain?
- OK to delete the stray root screenshot, or keep it?
