# AGENTS.md

## What this is
Static single-page site: no build, package manager, tests, lint, or CI — nothing to install.
Site copy is in Spanish; keep new text in Spanish.
- Entry point: `index.html` — 4 full-screen horizontal slides (nav `#1`–`#4`:
  principal, sobre nosotros, galería, contacto).
- Live site: https://narino-traveling.vercel.app/ — Vercel auto-deploys on push to `main`.

## Preview / verify
Open `index.html` in a browser (or serve the repo root with any static server).
After any edit, check at 1440 / 991 / 767 / 375 px: all 4 nav items, the 3 tabs
in slide 2, the owl carousel, and swipe/arrow-key navigation. Slides move by JS
transform (`.slides` translateX), not native scrolling.

## Architecture (non-obvious)
Script order in `index.html` matters:
jquery → bootstrap.bundle → owl.js → accordations.js → main.js.
- `assets/js/accordations.js` is misnamed: it's the full **jQuery UI 1.11.2** bundle.
  `main.js` calls `$("#tabs").tabs()` — do not delete or reorder it.
- Slider logic lives in `assets/js/main.js`: it indexes `$('.slide')` by DOM order,
  parses nav hrefs as `#` + one digit, and exposes `showSlide()` (guarded by a
  750 ms `transitioning` lock; keyboard arrows + touch swipe call it too).
- Slide backgrounds are keyed by `.slides .slide:nth-child(n)` in
  `assets/css/tooplate-main.css` (DOM position, not id).
- Adding/reordering a slide ⇒ update all three: a `.slide` div + nav `<li href="#N">`,
  `.slides { width: 400vw }` (100vw per slide), and the `:nth-child` background rules.
- Responsive breakpoints live only in `tooplate-main.css`: 991px (top bar header),
  767px, 575px (icons-only header). Content boxes (`.content`) scroll internally;
  `html/body` stay `overflow: hidden` by design.
- Libraries are vendored (`vendor/`: Bootstrap 4.1.3, jQuery 3.3.1); no CDN, no npm.
- Template origin: Tooplate "Earth" (`ABOUT THIS TEMPLATE.txt`) — editing ok,
  redistributing as a template not ok.

## Gotchas
- Commit `71933eb` (Nov 2023) once deleted slide 2's tab panels and 3 closing `</div>`s,
  nesting slides 3–4 inside slide 2. That was restored; if you edit near slide 2, run a
  nesting check (all 4 `.slide` must be direct children of `.slides`) before finishing.
- Contact form `#contact` has no backend (`action=""`, no JS handler) — submissions
  go nowhere. Don't add server code unprompted.
- The Google Fonts link loads Open Sans + Roboto; CSS font stack is "Open Sans".
- Headless Chrome clamps `--window-size` width to ≥500px, so a `--screenshot` at
  375px silently captures a cropped 500px layout. Verify true mobile widths via
  CDP `Emulation.setDeviceMetricsOverride` (or assert e.g. `.tabs-content` right
  edge ≈ viewport − 10px).
- Console 404 for `/favicon.ico` on first load is expected (no favicon exists).
- Commit style: informal Spanish messages; branches `main` (deployed) and
  `develop_NicoMay`. Pushing to `main` publishes the site.
