# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Sergio Cano's personal blog: a Jekyll site using the `mmistakes/minimal-mistakes` remote theme, deployed to GitHub Pages under the `/blog` baseurl. Content topics are Scala, Microservices, JVM, and general software engineering.

## Commands

Install dependencies:
```
bundle install
```

Serve locally with live reload (site available at `http://localhost:4000/blog/`):
```
bundle exec jekyll serve
```

Build only:
```
bundle exec jekyll build
```

There are no tests, linters, or CI config in this repo — changes are verified by running the site locally and checking pages render correctly.

## Architecture

- **Theme is external**: `_config.yml` sets `remote_theme: mmistakes/minimal-mistakes`. Layouts, includes, and default styling live in that remote gem, not in this repo — only overrides placed in matching local paths (e.g. `_layouts/`, `_includes/`, `_sass/`) take effect. There is currently no local override of the theme, so all layout/styling changes require either editing `_config.yml` settings (skin, etc.) or adding override files following minimal-mistakes' documented structure.
- **Content**:
  - `_posts/` — published posts, filename-dated (`YYYY-MM-DD-title.md`), permalink pattern is `/:categories/:title/` (set in `_config.yml`).
  - `_drafts/` — unpublished/in-progress posts (undated filenames); not built unless Jekyll is run with `--drafts`.
  - `_pages/` — standalone pages (About, 404, category/tag/year archives). `_config.yml` explicitly `include`s `_pages` since Jekyll doesn't process underscore-prefixed dirs by default.
  - `_data/navigation.yml` — drives the top nav (`main`: Posts, Tags, About).
- **Site settings** (`_config.yml`) control pagination (5 posts/page), Google Analytics (gtag), and Algolia search (`jekyll-algolia` plugin) — search config/API keys, if any, are not in this repo's tracked files.
- `index.html` is intentionally near-empty; homepage layout comes from the theme's `home` layout — don't add content directly here, override the theme layout instead.
- Images referenced by pages/posts live under `images/` and `assets/images/`.
