# Documentation

Documentation site for the **Gamifikált Önfejlesztő Webalkalmazás** (2025-27 Szakdolgozat), built with [Just the Docs](https://just-the-docs.com) and deployed with GitHub Actions.

## Deploy

1. Push this folder to the `main` branch.
2. Repo **Settings → Pages → Build and deployment → Source: GitHub Actions**.
3. Every push to `main` rebuilds the site (see the Actions tab).

## Structure

| Page | Hungarian | English |
|------|-----------|---------|
| Home | `hu/index.md` | `en/index.md` |
| Links | `hu/linkek.md` | `en/links.md` |
| Test documentation | `hu/tesztdokumentacio.md` | `en/test-documentation.md` |
| Contributors | `hu/kozremukodok.md` | `en/contributors.md` |

The root URL redirects to the Hungarian home page.

## Adding a page

Create it in both `hu/` and `en/` with the **same `ref`** in the front matter; the HU/EN switch uses `ref` to pair pages. The language is set automatically by folder.

```yaml
---
title: Új oldal
nav_order: 5
ref: new-page
---
```

## Local preview (optional)

```bash
bundle install
bundle exec jekyll serve
```
