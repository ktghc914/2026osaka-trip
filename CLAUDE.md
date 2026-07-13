# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Static single-page site for GitHub Pages: a personal 7-day/6-night Osaka trip itinerary. No build system, no package manager, no dependencies — `index.html` is the entire site (inline CSS in `<style>`, inline JS in `<script>`).

## Commands

There is no build, lint, or test tooling. To preview locally, just open `index.html` in a browser, or serve the directory:

```
python3 -m http.server 8000
```

To deploy, push to the `main` branch of the GitHub repo with GitHub Pages enabled (serving from `/` or `/docs`, whichever the repo is configured for).

## Architecture

Everything lives in one file, `index.html`, structured as:

- **Sticky header nav** (`header.site-header`): anchor links to each section (`#flight`, `#hotel`, `#checklist`, `#day1`…`#day7`). On narrow viewports the nav collapses into a hamburger toggle (`#menuToggle` / `#mainMenu`, driven by the `.open` class).
- **Sections** (`section.page`): one per nav item, in the same order as the nav links. Each currently holds `.placeholder` blocks marked "待補" (to be filled in) — this is where real itinerary content (flight info, hotel info, shopping checklist, daily plans) gets added.
- **Scroll-spy script**: an `IntersectionObserver` watches all `section.page` elements and toggles `.active` on the matching nav link as the user scrolls, so the nav highlights the current section.

When adding a new nav item/section, keep three things in sync: the `<a href="#x">` in `nav.menu`, the `<section id="x">` in the same order, and nothing else — the scroll-spy and mobile-menu JS already generalize over all `section.page` / `nav.menu a` elements without needing per-section changes.

Content style: minimalist/modern (warm off-white background, single accent color `--accent`, rounded cards). Color and spacing tokens are defined as CSS custom properties at the top of the `<style>` block (`--bg`, `--card`, `--ink`, `--muted`, `--accent`, `--accent-soft`, `--border`, `--radius`) — reuse these rather than hardcoding new colors.
