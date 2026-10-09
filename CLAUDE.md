# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Static website for **CIGRE Cyprus National Committee** (https://cigre-cyprus.org), built with Hugo and the Hugo Blox "blox-bootstrap" framework (the active successor of Wowchemy v5). Deployed to Netlify.

## Commands

Hugo version is pinned to **0.135.0** in `netlify.toml`. The site builds cleanly on Hugo ≥ 0.135 — anything older lacks the APIs (`transform.Unmarshal`, pagination object form) that the current module versions require.

- Local dev server (with drafts and future-dated content): `hugo server -D -F`
- Production build (mirrors Netlify): `hugo --gc --minify`
- Deploy preview build (includes future-dated events): `hugo --gc --minify --buildFuture -b $DEPLOY_PRIME_URL`
- Refresh module versions after editing `config/_default/module.yaml`: `hugo mod tidy`

**ALWAYS run `hugo --gc --minify` locally and confirm it succeeds before committing or pushing.** Netlify build minutes are scarce — a failed remote build wastes a credit and the fix-and-retry doubles the cost. Local compilation is the gate; don't push otherwise.

Note: production builds exclude future-dated events (e.g. seminars whose `date:` hasn't arrived yet). Use `--buildFuture` to preview them locally; deploy previews on Netlify get the flag automatically.

There are no tests, linters, or formatters configured. `.editorconfig` enforces 2-space indent, LF line endings, and UTF-8.

## Architecture

### Hugo Blox module system

This is **not** a classic `themes/` Hugo site. The framework is loaded as a Hugo Module via `go.mod` and `config/_default/module.yaml`:

- `github.com/HugoBlox/hugo-blox-builder/modules/blox-bootstrap/v5` — the page-builder framework (legacy Bootstrap-based widgets, the direct successor of `wowchemy/v5`)
- `github.com/HugoBlox/hugo-blox-builder/modules/blox-plugin-decap-cms` — Decap CMS integration (replaces the old `wowchemy-cms`)
- `github.com/HugoBlox/hugo-blox-builder/modules/blox-plugin-netlify` — Netlify-specific output formats

To override framework templates, place files at the matching path under `layouts/`. The active override is `layouts/partials/blocks/v1/timeline.html` — a custom widget block (the framework doesn't ship one). Blocks receive `.wcPage` and `.wcBlock` (not the old `.root`/`.page`).

There is also a newer Hugo Blox "kit" (Tailwind-based, at `github.com/HugoBlox/kit`) — that's a completely different architecture and would require rewriting every widget. Stay on `blox-bootstrap/v5` unless deliberately migrating.

### Page-builder homepage and widget pages

`content/home/` is the homepage. Each file (`welcome.md`, `slider.md`, `events.md`, `news.md`, `contact.md`) is a **widget section** with `headless: true` and a `widget:` type (e.g. `hero`, `pages`, `slider`). The `weight:` field controls section order. To link to a section from the navbar, use a hash + filename (e.g. `#contact` in `config/_default/menus.yaml` → `content/home/contact.md`).

The same pattern is used for `content/conference/` — `index.md` is the widget_page parent, and sibling `headless: true` files (`welcome.md`, `program.md`, `timeline.md`, etc.) are composed into a single page.

### Configuration

Under `config/_default/`:
- `hugo.yaml` — Hugo core (baseURL, taxonomies, permalinks, pagination)
- `module.yaml` — Hugo Module imports (the three blox modules above)
- `params.yaml` — site behavior (appearance, SEO, analytics, features)
- `menus.yaml` — top navbar entries
- `languages.yaml` — i18n setup (site is English-only currently)

Active theme is `appearance.theme_day` in `params.yaml`, which references a file in `data/themes/`. The custom **`cigre`** theme (`data/themes/cigre.toml`) sets the brand green `#007e4f`. SCSS overrides live in `assets/scss/template.scss`.

### Content sections

Each top-level folder under `content/` (`event/`, `post/`, `authors/`, `conference/`, `people/`, `publication/`, `about/`) is a Hugo section. Items are **page bundles** — each is a directory containing `index.md`, `featured.png` (cover), and any attached assets (PDFs, slides). Event frontmatter expects `date`/`date_end`, `location`, and `url_pdf`/`url_slides`/`url_video` for media links. Authors referenced in frontmatter must exist as folders under `content/authors/` (the slug becomes the URL via the `authors: '/author/:slug/'` permalink).

### Shortcodes that need page-bundle resources

The `{{< table path="…csv" >}}` shortcode now resolves the path through `.Page.Resources` — the CSV must be a resource of the page bundle calling it. This breaks the old pattern of dropping a CSV next to a `headless: true` widget block (where `.Page` is the block, not the parent bundle). Inline the table as Markdown instead, or move the CSV into a real leaf bundle.

### Netlify redirects

`netlify.toml` contains a small set of short-link redirects (e.g. `/profile`, `/2023_NC_photos`) that point to external services or PDFs in `static/`. When adding event registration links or external resources, prefer adding a redirect here over hardcoding long URLs in content.

### Static assets vs. Hugo assets

- `static/` — served verbatim at the site root (PDFs like `Cyprus_NC_Profile.pdf` are linked from redirects).
- `assets/` — processed by Hugo Pipes (SCSS in `assets/scss/`, images in `assets/media/`).
