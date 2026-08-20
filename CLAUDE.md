# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Sebastian Cifuentes' personal academic website, built with Hugo (v0.147.x, extended) on a customized fork of the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme via the [pmichaillat/hugo-website](https://github.com/pmichaillat/hugo-website) academic template. The theme is vendored directly under `themes/PaperMod` (plain committed files, not a git submodule, despite the deploy workflow passing `submodules: recursive`).

## Commands

- `hugo server` — run the local dev server at http://localhost:1313 with live rebuild. Run from this directory (`scifuentes14.github.io`).
- `hugo --minify --baseURL "https://scifuentes14.github.io/"` — production build, matching what CI runs (output goes to `public/`, gitignored).
- `hugo new --kind paper content/papers/<slug>/index.md` / `hugo new --kind course content/courses/<slug>/index.md` — scaffold a new paper or course page from the custom archetypes in `archetypes/`.

There is no linter, test suite, or package.json in this repo — validation is "does it build and does it look right locally."

## Deployment

Pushes to `main` trigger `.github/workflows/hugo.yml`, which builds with Hugo (installed via `.deb`) + Dart Sass on `ubuntu-latest` and deploys `public/` to GitHub Pages. There is no staging environment; pushing to `main` is a live deploy.

## Architecture

**Two content models coexist:**
1. `content/papers/*` and `content/courses/*` are the template's original structured collections (front matter driven, rendered through `layouts/_default/list.html`/`single.html`, using the archetypes in `archetypes/`). These still contain the upstream template's placeholder "von Igelfeld" demo content and are not the live research/teaching pages.
2. `content/research.md` and `content/teaching.md` are hand-authored pages with inline HTML/CSS/JS (e.g. `toggle_visibility()` for collapsible abstracts in `research.md`) — these are the actual live Research and Teaching pages linked from the site nav, and don't use the papers/courses collection pattern. When updating research or teaching content, edit these files directly rather than the `papers`/`courses` collections.

**Homepage** (`content/_index.md` + `layouts/index.html`) uses a custom two-column `academic-layout` (avatar/name/location/affiliation sidebar + bio content), overriding PaperMod's default `profileMode` home layout entirely — `profileMode` is disabled in `config.yml`.

**Styling**: `assets/css/extended/academic-home.css` is a full custom skin layered on top of PaperMod's base CSS (`assets/css/common/*`, `assets/css/core/theme-vars.css`), giving the site a UC San Diego color scheme. Custom CSS variables (`--ucsd-navy`, `--ucsd-blue`, `--ucsd-gold`, etc.) are defined in `assets/css/core/theme-vars.css` for both light and dark `:root` blocks and wired into PaperMod's own variables (`--primary`, `--content`, `--darkcolor`) there — even though the dark-mode theme toggle is disabled (`disableThemeToggle: true`, `defaultTheme: light`).

**Site config** (`config.yml`) drives nav (`menu.main`), the sidebar bio (`params.sidebar`), and social icons (`params.socialIcons`, including a link to the CV PDF in `static/`). Large binary assets (CV, job market paper, slides, headshots) live in `static/` and are referenced by absolute path (e.g. `/NewCV_SC_2026-07-28.pdf`) from content pages.

**Archive page** (`content/archive.md`) uses the theme's `archives` layout (`layouts/_default/archives.html`) to auto-list all content in reverse-chronological order.
