# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Deployment

- **GitHub:** `https://github.com/sato-ryoichi-lp/sato-ryoichi-lp-` (branch: `main`)
- **Vercel:** Connected to above repo. Push to `main` → auto-deploy.
- To deploy changes: `git add . && git commit -m "message" && git push`

## Development Server

```bash
cd "/Users/yusukeyamamoto/Downloads/AI事業/いっちーLP"
python3 -m http.server 8080 --bind 0.0.0.0
```

- Desktop: `http://localhost:8080`
- Mobile (same Wi-Fi): `http://<local-ip>:8080` — get IP with `ipconfig getifaddr en0`

## Architecture

Single-page landing page with two full-screen sections. No build step — plain HTML/CSS/JS.

**Files:**
- `index.html` — all markup + inline JavaScript
- `style.css` — all styles including animations and responsive overrides
- `images/profile-nobg.png` — RGBA PNG hero person photo (transparent background)
- `顧客事例/` — 16 customer testimonial JPG images used in carousel

**Design system:**
- Base design: 1600×676px → `1px = 0.0625vw` / `1px = 0.14793vh`
- All measurements use `vw`/`vh` units derived from these ratios
- Background color `#a0a0a0` + two `mix-blend-mode: multiply` gradient overlays recreates the Figma dark gradient effect
- Gold accent color: `#cdb218`

## Section Layout

**Section 1 (Hero):** `position: fixed` on desktop, `position: relative` on mobile. Contains:
- `.hero-headline` — main 2-line headline (Noto Sans JP)
- `.hero-subtitle` — sub-copy below headline ("誰かが勝手に作った...")
- `.hero-person` — person photo, right side desktop / bottom-right mobile
- `.hero-name-block` — name and role, lower right desktop / lower left mobile

**Section 2 (Testimonials):** `position: fixed` on desktop (starts `opacity: 0`, transitions in), `position: relative` on mobile. Contains infinite-loop carousel + gold CTA button linking to `https://lin.ee/w4WeWbt`.

## Section Switching

**Desktop:** JS-driven crossfade (`opacity` + `translateY` transition, 0.75s). Triggered by `wheel` event (900ms debounce). Both sections are `position: fixed` stacked at top:0 left:0. Section 2 starts at `opacity: 0; z-index: 9`; on switch it comes to `z-index: 10` and fades in.

**Mobile (≤768px):** CSS scroll-snap (`scroll-snap-type: y mandatory` on `html`, `scroll-snap-align: start` on sections). Sections revert to `position: relative` via `!important` overrides in the media query. Wheel/touch section-switching JS is skipped when `isMobile()` returns true.

## Carousel (Section 2)

Infinite loop via DOM cloning: last N cards prepended, first N cards appended. On `transitionend`, if `currentPage` is out of range, snap back without animation. Cards per page: 4 desktop / 2 mobile. All dimensions computed in JS from `vw` values to match CSS.

## Animations

Hero section: all elements (bg, person, headline, subtitle, name-block) fade in simultaneously with `fadeIn 1.0s ease 0.1s`.

Testimonial section: elements have `.anim` class (initial `opacity: 0`). JS adds `.is-visible` when switching to section 2, triggering `fadeUp` keyframe animations via CSS selectors like `.testimonial-section .anim.is-visible`.

## Responsive

Mobile overrides are in a single `@media (max-width: 768px)` block at the bottom of `style.css`.

- `.sp-br` — `display: none` desktop / `display: block` mobile, used for mobile-only line breaks in `.cta-message` and `.hero-subtitle`
- Hero section height: `66.67vh` mobile (desktop: `100vh` fixed)
- Testimonial section height: `72vh` mobile (desktop: `100vh` fixed)
- Person image: bottom-right anchored, `110vw × 90vh`, `object-fit: contain` mobile
