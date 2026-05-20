# CIGRE Cyprus NC Website

Source for [cigre-cyprus.org](https://cigre-cyprus.org), the official site of the CIGRE Cyprus National Committee. Built with [Hugo](https://gohugo.io/) and the [Hugo Blox](https://hugoblox.com/) `blox-bootstrap/v5` framework, deployed to Netlify.

## Local development

You need Hugo Extended ≥ 0.135.0 (Netlify pins **0.135.0** in `netlify.toml`).

```bash
hugo server -D -F                       # local preview with drafts & future events
hugo --gc --minify                      # production build (mirrors Netlify)
hugo --gc --minify --buildFuture        # include future-dated events
hugo mod tidy                           # refresh module versions after editing config/_default/module.yaml
```

> **Always run `hugo --gc --minify` locally before pushing.** Netlify build minutes are limited; a failed remote build wastes a credit.

The first build downloads the Hugo Blox modules into `~/.cache/hugo_cache/` — subsequent builds are fast.

## Editing content

### Add an event

Create a page bundle under `content/event/`:

```
content/event/2026.11-Webinar/
├── index.md           # frontmatter + abstract + speaker bio
├── featured.png       # cover image shown on the events grid
└── Speaker.pdf        # flyer / slides (optional)
```

Inside `index.md` use the existing events as templates. Key fields:

- `date:` / `date_end:` — RFC3339 timestamps; events with future `date:` only render in deploy previews and after the date passes.
- `location:` and `address:` — physical venue (use `Online` for webinars).
- `url_pdf:` — path to the flyer/slides PDF, written relative to the site root (e.g. `event/2026.11-webinar/Speaker.pdf`); Hugo lowercases the folder name in the URL.

### Edit the homepage

Each file in `content/home/` is a widget block (`headless: true`, `widget:` type, `weight:` for order). The same widget-page pattern is used for `content/conference/`.

### Add a redirect

For short links and external URLs, add a `[[redirects]]` block in `netlify.toml` instead of hardcoding long URLs in content.

## Repository layout

```
config/_default/    Hugo config (hugo.yaml, module.yaml, params.yaml, menus.yaml, languages.yaml)
content/            Site content (home/, event/, post/, conference/, authors/, …)
layouts/            Local template overrides (currently only the timeline block)
assets/             Hugo Pipes assets (SCSS in scss/, brand images in media/)
static/             Files served verbatim at the site root
data/themes/        Custom Hugo Blox colour themes (active: cigre.toml — brand green #007e4f)
netlify.toml        Build config + short-link redirects
```

See `CLAUDE.md` for architecture details (module system, widget pages, shortcode conventions).
