# QWEN.md — Context for ivanscm.name

## Project Overview

This is a **personal blog and portfolio website** built with [Hugo](https://gohugo.io/) (static site generator) and deployed via [GitHub Pages](https://pages.github.com/) (GitHub Actions).

- **Author:** Григорьев Иван (Ivan Grigoryev) — Full Stack Developer
- **Primary Language:** Russian (ru-ru)
- **Base URL:** https://ivanscm.github.io/
- **Theme:** Custom theme `ivan-blog` (located in `/themes/ivan-blog/`)
- **GitHub Repo:** `ivanscm/ivanscm.github.io` (User site)

### Tech Stack

- **Hugo** — Static site generator (Go-based)
- **GitHub Pages** — Deployment and hosting (via GitHub Actions)
- **Custom Hugo Theme** — `ivan-blog` with Bootstrap styling

### Content Structure

| Section | Path | Description |
|---------|------|-------------|
| Blog Posts | `/content/posts/` | Technical articles and notes |
| Books | `/content/books/` | Book library / reading list |
| Translates | `/content/translates/` | Translated articles |
| Categories | `/content/categories/` | Taxonomy pages |

### Key Configuration

- **Pagination:** 6 posts per page
- **Recent Posts on Homepage:** 3 posts (configurable via `recent_posts_number` param)
- **Disqus:** Enabled for comments (`ivanscm` shortname)
- **Markdown:** Unsafe HTML rendering enabled (`unsafe = true`)

## Building and Running

### Prerequisites

- [Hugo](https://gohugo.io/installation/) (extended version, see `.github/workflows/hugo.yaml` for pinned version)

### Development Server

```bash
hugo server -D
```

- Runs local dev server with draft posts enabled
- Auto-rebuilds on file changes
- Default URL: http://localhost:1313/

### Build (Production)

```bash
hugo
```

- Generates static files to `/public/` directory

### Deploy to GitHub Pages

Push to `main` triggers `.github/workflows/hugo.yaml`, which builds the site
and deploys it to GitHub Pages (`https://ivanscm.github.io/`).

- Pages source in repo settings: **GitHub Actions**
- HTTPS is enforced in Pages settings

## File Structure

```
ivanscm.name/
├── config.toml           # Hugo configuration
├── .github/workflows/    # GitHub Actions (Hugo build + Pages deploy)
├── content/              # Markdown content
│   ├── posts/            # Blog posts
│   ├── books/            # Book entries
│   ├── translates/       # Translated content
│   └── categories/       # Category taxonomy
├── themes/ivan-blog/     # Custom theme
│   ├── layouts/          # Hugo templates
│   │   ├── _default/     # Default templates
│   │   ├── partials/     # Reusable partials
│   │   └── posts/        # Post-specific templates
│   └── static/           # Theme assets
├── layouts/              # Site-specific overrides (empty)
├── static/               # Site-wide static assets
├── data/                 # Data files (JSON, YAML, TOML)
├── resources/            # Hugo resource cache
└── public/               # Generated output (gitignored)
```

## Content Front Matter

### Post Template

```yaml
---
title: "Заголовок"
date: 2020-04-12T13:06:44+07:00
draft: false
type: "posts"
categories: ["tools", "other", "work"]
featured_image: "posts/image.png"
images: ["posts/image.png"]
---
```

### Summary Break

Use `<!--more-->` to define summary cut-off point for post listings.

## Theme Partials

The custom theme includes these partial templates:

- `navbar.html` — Navigation bar
- `header.html` / `footer.html` — Page header/footer
- `summary-with-image.html` — Post card with image
- `summary-book.html` — Book entry card
- `summary-translate.html` — Translation card
- `category-link-with-icon.html` — Category badges
- `pagination.html` — Pagination controls
- `metrika.html` — Yandex Metrica integration

## Development Conventions

### Content Guidelines

- All content written in **Russian**
- Use `hugo new posts/FILENAME.md` to create new posts
- Set `draft: true` for works in progress
- Include `images` array for social sharing (Open Graph)

### Code Blocks

- Syntax highlighting enabled via Hugo/Goldmark
- Use fenced code blocks with language specifier
- Example:

  ````markdown
  ```php
  <?php echo "Hello"; ?>
  ```
  ````

### Categories

Common categories used: `tools`, `other`, `work`, `technologies`, `management`

## Deployment Notes

- GitHub repo: `ivanscm/ivanscm.github.io` (public User site)
- Workflow `.github/workflows/hugo.yaml` builds with pinned Hugo extended version and deploys via `actions/deploy-pages`
- `.hugo_build.lock` file indicates build in progress (auto-generated, gitignored)
- `/public/` is a build artifact (gitignored, never committed)

## Social Links

Configured in `config.toml`:

- GitHub: https://github.com/ivanscm
- Habr: https://habr.com/ru/users/ivanscm/
- Telegram: https://t.me/prog_codex

## Notes (from README)

Planned/ongoing work items:
- Блок кода (code block improvements)
- Заметка о планировании (planning notes)
- Страница "Обо мне" с CV (About page with resume)
- Перенос в Селектел (migration to Selectel hosting)
- Англоязычная версия (English version of the site)
