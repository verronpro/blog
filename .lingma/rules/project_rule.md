# Project: Joseph Verron — Blog (Jekyll)

Audience for this document: advanced contributors who will maintain and evolve the blog. Keep this as the single place
to remember the decisions and the sharp edges of this repository.

## Build and Configuration Instructions

### Stack

- Static site generator: Jekyll (Ruby).
- Content formats used today: Markdown only — for both published posts (`_posts/*.md`) and drafts (`_drafts/*.md`).
- Data-driven settings: `_config.yml`, `_data/*.yml`.

### Prerequisites

- Ruby (>= 3.0 recommended) and Bundler installed.
- On Windows, use RubyInstaller with MSYS2. Ensure `ridk install` is run to provision dev tools.

### First-time setup

1) Install gems
    - `bundle install`
2) Serve locally with live-reload
    - `bundle exec jekyll serve --livereload`
    - Site will be at http://127.0.0.1:4000 (watch console for the exact URL).
3) Building for production
    - `JEKYLL_ENV=production bundle exec jekyll build` (on PowerShell:
      `$env:JEKYLL_ENV = 'production'; bundle exec jekyll build`).

### Drafts workflow

Drafts live in `_drafts/` (Markdown only). These are NOT published, but Jekyll will render them in dev when using
`--drafts`.

Preview drafts locally:

- `bundle exec jekyll serve --drafts` (works out of the box with `_drafts/`).
- Alternatively, temporarily move a draft to `_posts/` and give it a valid filename/front matter. Revert before
  commit if not ready.

### Content format policy

Markdown-only site: AsciiDoc is not supported in the build. If you have a legacy `.adoc` draft, convert it to
Markdown before committing. Suggested path: `asciidoctor -b docbook INPUT.adoc` then
`pandoc -f docbook -t gfm -o OUTPUT.md` (verify code blocks and admonitions by hand).

### Configuration touch points

`_config.yml`:
: site title, baseurl, url, plugins, markdown/asciidoc engines.

`_data/`:

- `authors.yml`:
  : author metadata (Joseph and guests). Ensure slugs used in front matter match entries here.

- `navigation.yml`:
  : header/footer menus. Update when adding new sections.

- `variables.yml` and `locale.yml`:
  : site-wide strings and localization knobs.

- `licenses.yml`:
  : per-post license choices if used in front matter.

- Includes and assets:
  : `_includes/**` (favicons, SVG logo). Keep favicon paths intact if theme/layout expects them.

### Publishing

Standard GitHub Pages/Jekyll workflow: push to default branch; GitHub Pages builds and serves.

If building locally for verification, ensure the same gems as production are used; avoid system-wide Jekyll.

## Writing Style Guidelines (Joseph Verron, software craftsmanship; docs-as-code; Office‑stamper maintainer)

### Voice and tone

Authoritative but practical; prefer code-backed explanations, short examples, and links to canonical sources.

Bias toward “docs as code” practices: show configuration, scripts, and checklists that readers can copy.

Keep paragraphs short (max 4–5 lines). Use lists for sequences and checklists.

English by default. If bilingual content is added, keep one post per language; do not mix.

### Post types and expectations

1) Monthly Commit (project maintenance journal)
    - Purpose: document a concrete, scoped improvement (e.g., “replace in table cell”, “Java migration”, “custom
      functions”).
    - Pattern:
        - Context (1–2 paragraphs): the problem and why it matters.
        - Change summary: bullets of significant decisions, trade-offs, and before/after.
        - Code/core snippets: minimal but runnable or reproducible fragments.
        - Impact: a measurable outcome (performance, reliability, API clarity).
        - Next steps: what remains or is intentionally deferred.
    - Include links to commits/PRs and to the corresponding release notes if applicable.

2) Monthly Topic (deeper practice write-up)
    - Purpose: craft knowledge on a technique (e.g., template invariants, resolver safety, docs pipeline).
    - Pattern: thesis → technique taxonomy → worked example(s) → pitfalls → checklist.
    - Include diagram-as-code snippets if relevant (Mermaid, PlantUML, or AsciiDoc blocks).

3) How-to / Guide (task-focused)
    - Purpose: walk a reader from problem to solution in reproducible steps.
    - Pattern: prerequisites → steps (numbered) → verification → troubleshooting.
    - Provide CLI invocations and exact output fragments; prefer copy-paste friendly blocks.

4) Release Notes for Office‑stamper
    - Purpose: communicate changes to the small Java library.
    - Pattern: headline changes → breaking changes (prominent) → migration notes (with code) → deprecations →
      links to issues/PRs.
    - Add “Since/Removed/Deprecated” tables for APIs affected. Show a minimal diff or before/after code.

5) Talks / Workshops / Presentations-as-code
    - Purpose: publish materials and a narrative.
    - Pattern: abstract -> slide deck/source links -> demo instructions -> references.
    - Prefer storing code samples in a dedicated repo and linking; keep the post lightweight.

6) Docs-as-Code Practices
    - Purpose: share pipelines and governance (checklists, CI scripts, conventions).
    - Pattern: problem framing -> policy -> implementation (scripts/config) -> enforcement (CI) -> exceptions.

### Style mechanics

Titles:
: Concise, action-oriented. Avoid clickbait.

Front matter:
: Include `title`, `date`, `tags` (small, consistent set), `categories` (only one), `author: Joseph` (match
`authors.yml`), and `license` if applicable.

Code blocks:
: Always specify language when possible. Use fenced blocks. Include inputs and outputs for CLI.

Diagrams:
: Prefer diagram-as-code blocks. Host SVG/PNG in-repo if needed; keep sources alongside images.

Citations:
: Link to upstream sources (specs, repos). Use canonical URLs.

## Development Information (structure, conventions, styling)

### Repository layout essentials

- `_posts/`: only published Markdown with Jekyll-compliant filenames: `YYYY-MM-DD-slug.md`.
- `_drafts/`: working area for upcoming posts; Markdown `.md` only. Works with `jekyll serve --drafts`.
- `_data/*.yml`: structured data. Keep keys kebab-case or snake_case consistently; avoid spaces.
- `_includes/`: shared snippets (favicons, SVG). Avoid hardcoding paths in posts.
- Root pages: `index.md`, `about.md`, `archive.md`.

### Front matter contract

Minimal set:

- `layout: article` (or site default if set), `title: ...`, `date: YYYY-MM-DD`, `tags: [a, b]`,
  `author: Joseph`.

Optional: `description`, `image`, `license`, `toc: true` (if theme supports), `permalink` for custom URLs.

Keep dates in UTC to avoid ordering surprises in Jekyll.

### Filenaming and slugs

Slug is the dash-separated part after the date. Keep consistent between filename and front matter `permalink` when
overridden.

For series, prefix with a common slug stem (e.g., `monthly-commit-...`).

### Images and assets

Prefer an `assets/` (or `images/`) subtree per post: `assets/YYYY/MM/slug/` for local images and attachments.

Use web-friendly SVG/PNG. Optimize images before commit. Store diagram sources (.puml, .mmd, .drawio) alongside outputs.

### Linking

Use relative links when pointing within the site. For GitHub source, link to specific commits or permalinks, not
branches.

Avoid absolute `http://localhost:` links in committed content.

### Graphical styling

Logo SVG lives in `_includes/svg/logo.svg`; favicons under `_includes/head` and root `favicon*.png/ico` plus
`browserconfig.xml` and `ms-icon*` variants.

Custom CSS entry point: `assets/css/custom.css` is linked from `_includes/head/favicon.html`. Use this file to
define design tokens and light component styling; avoid heavy theme overrides.

Design tokens (CSS variables) currently defined: `--cc-primary`, `--cc-accent`, `--cc-text`, `--cc-muted`,
`--cc-bg`, `--cc-surface`, `--cc-border`.
- Components styled: `.btn`, `.btn-primary`, `.btn-ghost`, `.tag` `.cards`, `.card`,
  `.posts-grid`, `.post-card`, `.post-meta`, `.site-header*`.
- Dark mode: tokens switch via `@media (prefers-color-scheme: dark)`; surfaces and borders adapt automatically.
- Print stylesheet: `@media print` hides nav/footer/buttons, removes chrome, and appends URLs to links.

Reusable includes for presentation live under `_includes/home/*`, `_includes/post/*`, and `_includes/footer/*`:
- `_includes/home/hero.html`: top landing hero with CTA. `_includes/home/cards.html`: 3 services cards.
- `_includes/footer/custom.html`: site footer with quick links and social.

Header include: `_includes/header/cta.html` provides a sticky header with simple nav and a persistent “Hire me”
CTA. Include it at the top of pages if the theme layout doesn’t already render a header, or when you want this
variant.

Structured data: `_includes/head/structured-data.html` injects JSON‑LD (`WebSite` for general pages, `BlogPosting`
for posts). It is included from `_includes/head/favicon.html` so it is available across the site.

Keep palette and typography consistent with the theme. Prefer system font stacks for performance; ensure WCAG AA
contrast.

### Linting and sanity checks (manual, lightweight)

Run local build before pushing: `bundle exec jekyll build`.

Validate links with a one-off tool when posts are heavy with references (e.g., `htmlproofer` or a simple script).
Not enforced here by default.

### Adding a new post (checklist)

1) Pick type (see section 2) and draft under `_drafts/`.
2) Ensure front matter meets the contract; set accurate `date`.
3) Add images under `assets/YYYY/MM/slug/` if needed; update paths.
4) Run locally; fix build warnings.
5) Move to `_posts/` with final filename on publish.
6) If navigation needs an entry, update `_data/navigation.yml`.
7) If post relates to Office‑stamper, link to the exact tag/release and migration notes.

### Taxonomy

Keep tags small in number and consistent across posts: `docs-as-code`, `office-stamper`, `java`, `templates`,
`testing`, `toolchain`, `ci`, `diagram-as-code`, etc.

Avoid introducing near-duplicates (e.g., `doc-as-code` vs `docs-as-code`).

### Content previews and screenshots

Prefer textual demonstrations to screenshots. If screenshots are necessary, annotate them and keep them small.
Store originals.

### Internationalization

`_data/locale.yml` exists; if you introduce multi-language support, document the pattern (e.g., `/fr/` prefix) and
update navigation accordingly.

## Operational Notes and Sharp Edges

Markdown-only policy: New content must be `.md`. Legacy `.adoc` drafts should be converted to Markdown prior to
commit. Verify fenced code blocks, tables, and admonitions post-conversion.

Do not rename `_posts/` or `_data/` without checking Jekyll expectations and GitHub Pages compatibility.

If adding CI (link check, build), prefer minimal GitHub Actions with `ruby/setup-ruby` and
`bundle install --deployment` to mirror production.

Renovate config (`renovate.json`) exists at root; if adding Ruby dependencies automation, extend it carefully to avoid
surprise PRs.

## Example front matter templates

```yaml
---
layout: article
title: "Monthly Commit — Paragraph Replace API"
date: 2025-10-14
categories: [office-stamper]
tags: [ office-stamper, java, api ]
author: Joseph
description: Improving the paragraph replace API to clarify contracts and remove Lombok leftovers.
license: cc-by-4.0
---
```

```yaml
---
layout: article
title: "Docs-as-Code — Checklists for Solo Maintainers"
date: 2025-08-21
categories: [ documentation-as-code ]
tags: [ documentation-as-code, governance, ci ]
author: Joseph
toc: true
---
```

## House rules

- Keep this file updated whenever you change build, plugins, or content conventions.
- Prefer small, atomic commits; include a short rationale in commit messages. Link to issues or ADRs if relevant.
