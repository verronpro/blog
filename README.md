# Joseph Verron — Blog (Jekyll)

A static blog for software craftsmanship and docs‑as‑code topics. Built with Jekyll and served via GitHub Pages. Content
is Markdown‑only; diagrams are rendered client‑side to remain compatible with GitHub Pages (no custom plugins required).

Overview

- Static site generator: Jekyll (via github-pages gem)
- Language/stack: Ruby, Bundler
- Content: Markdown under `_posts/` and `_drafts/`
- Hosting: GitHub Pages
- Base URL: `/blog` (see `_config.yml`)

Requirements

- Ruby (>= 3.0 recommended) and Bundler
    - Windows: use RubyInstaller with MSYS2; run `ridk install` to provision dev tools
- Git

Setup

1) Install dependencies
    - `bundle install`
2) Serve locally with live reload
    - `bundle exec jekyll serve --livereload`
    - Drafts too: `bundle exec jekyll serve --livereload --drafts`
    - Site will be at http://127.0.0.1:4000 (watch the console for the exact URL)
3) Production build
    - PowerShell: `$env:JEKYLL_ENV = 'production'; bundle exec jekyll build`

Common Scripts and Commands

- Start dev server: `bundle exec jekyll serve --livereload`
- Start dev server incl. drafts: `bundle exec jekyll serve --drafts --livereload`
- Build (dev): `bundle exec jekyll build`
- Build (prod): `$env:JEKYLL_ENV = 'production'; bundle exec jekyll build`

Environment Variables

- JEKYLL_ENV: set to `production` for optimized builds (minification, etc.). Default is `development`.
- TODO: Document any additional env vars if introduced later (e.g., analytics toggles). None required today.

Project Structure

- `_posts/` — published posts, filenames `YYYY-MM-DD-slug.md`
- `_drafts/` — unpublished drafts (served with `--drafts`)
- `_data/` — site data (`authors.yml`, `navigation.yml`, etc.)
- `_includes/` — partials (head, header, footer, structured data, SVG, home sections)
- `_layouts/` — page layouts
- `assets/` — CSS and static assets (custom styles in `assets/css/custom.css`)
- `index.md`, `about.md`, `archive.md` — root pages
- `_config.yml` — site configuration (title, url/baseurl, plugins)
- `Gemfile` — Ruby gems (github-pages, jekyll-feed, Windows helpers)
- `_site/` — generated site output (do not edit; created by Jekyll)

## Diagrams as Code pipeline

This site renders diagrams client‑side to stay compatible with GitHub Pages (no custom Jekyll plugins required).

Supported authoring patterns:

- Mermaid: fenced blocks
- PlantUML and Graphviz/DOT: rendered via Kroki (https://kroki.io) using client‑side compression.

How to write diagrams

1) Mermaid with fenced code (auto‑enhanced):
    ````markdown
    ```mermaid
    flowchart TD
      A --> B
    ```
    ````
2) PlantUML (via Kroki), fenced block (auto‑enhanced):
    ````markdown
    ```plantuml
    @startuml
    Alice -> Bob: Hello
    @enduml
    ```
    ````
3) Graphviz/DOT (via Kroki), fenced block (auto‑enhanced):

    ````markdown
    ```dot
    digraph G { A -> B }
    ```
    ````

Notes

- Rendering happens in the browser. Internet access to cdn.jsdelivr.net and kroki.io is required for full rendering.
- Dark mode is supported through CSS tokens; diagrams are embedded as SVGs where possible.
- Print styles collapse the two‑column layout to a single column.

Content and Writing Conventions

- Markdown‑only for posts and drafts.
- Drafts live under `_drafts/`; use `--drafts` to preview locally.
- Keep dates in UTC in front matter to avoid ordering surprises.
- See `_data/*.yml` for navigation, authors, and variables used across the site.

Entry Points

- Local dev server uses Jekyll’s built‑in server (`bundle exec jekyll serve`).
- GitHub Pages builds the site on push using the `github-pages` gem versions; avoid custom plugins.

License
The repository uses CC0 1.0 Universal (see LICENSE). Content and code in this repo are released to the public domain to
the extent possible under the law.

Maintenance Notes

- Dependabot/Renovate: A `renovate.json` exists; extend carefully if adding Ruby dependency automation. (See repo root.)
- TODO: Document theme customizations and any layout decisions if they evolve.
