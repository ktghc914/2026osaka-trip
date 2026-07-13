# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Static multi-page site for GitHub Pages: a personal 7-day/6-night Osaka trip itinerary. No build system, no package manager, no dependencies — plain HTML pages (`index.html`, `flight.html`, …) sharing one stylesheet (`styles.css`); each page inlines its own `<script>`.

## Commands

There is no build, lint, or test tooling. To preview locally, just open `index.html` in a browser, or serve the directory:

```
python3 -m http.server 8000
```

To deploy, push to the `main` branch of the GitHub repo with GitHub Pages enabled (serving from `/` or `/docs`, whichever the repo is configured for).

## Architecture

- **`styles.css`**: shared stylesheet linked by every page (`<link rel="stylesheet" href="styles.css">`). Holds the CSS custom properties (`--bg`, `--card`, `--ink`, `--muted`, `--accent`, `--accent-soft`, `--border`, `--radius`), the sticky-header/nav rules, `.hero`, `.nav-card`/`.nav-cards` (homepage card grid), `.card`/`.flight-grid` (per-page content cards), and `section.page` spacing. Reuse these tokens/classes rather than hardcoding new colors or duplicating card styles.
- **`index.html`**: the homepage. `header.site-header` has the sticky nav (brand logo linking to `index.html`, plus a hamburger toggle `#menuToggle`/`#mainMenu` on narrow viewports, driven by the `.open` class). `<main>` holds a `.hero` title and a `.nav-cards` grid — one `.nav-card` per topic (機票/飯店/購買清單/day1–7), each linking either to its own page (e.g. `flight.html`) once that page exists, or to an in-page `#anchor` placeholder until it does.
- **Per-topic pages** (e.g. `flight.html`): same header/footer/`<script>` scaffolding as `index.html` (copy-pasted — there's no templating without a build step), linking the same `styles.css`. `<main>` has a `.hero`-style page title — starting with a `.back-link` ("← 回首頁" linking to `index.html`, every non-homepage page must have one) — followed by one or more `section.page` blocks holding `.card` content. The current page's own nav link gets `class="active"` hardcoded; the rest point back to `index.html#anchor` for topics not yet split into their own page.
- **Scroll-spy script** (inlined per page): an `IntersectionObserver` watches that page's `section.page` elements and toggles `.active` on the matching nav link as the user scrolls — relevant when a page has multiple sections; a no-op on pages with none.

When a new topic gets its own page, follow the `flight.html` pattern: copy the header/footer/script scaffolding, link `styles.css`, hardcode `class="active"` on that page's own nav item, and update the homepage's matching `.nav-card` href from `#anchor` to the new page.
