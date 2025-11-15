---
layout: page
title: Ideas & Ongoing Projects
permalink: /ideas.html
description: A living list of ongoing work and ideas I’d like to explore when time allows — from docs-as-code tooling to Office automation experiments.
aside:
  toc: true
---

This page tracks small initiatives I’m actively exploring and a backlog of ideas I’d like to tackle. If you want to collaborate on any of these, or if a topic would help your team, reach out via LinkedIn (see Operating Model) or GitHub issues.

Tip: Items marked with “seeded from Resources” originate from the curated list on [/resources.html](/resources.html).

# Ongoing projects

- Office‑stamper improvements (Java)
  - Scope: tighten template semantics (placeholders, invariants, pre/post‑processing) and expand test coverage for .docx and .pptx. See Monthly Commit drafts in `_drafts/`.
  - Status: active; several drafts in progress (e.g., comment retrieval, resettable iterators, pre‑processing of placeholders).

- Docs‑as‑Code pipelines for diagrams  — seeded from Resources
  - Scope: standardize PlantUML/Mermaid/Graphviz flows via Kroki; ensure diagrams build in CI alongside docs.
  - Status: ongoing; looking to publish a minimal starter and a checklist.

- Site hygiene and authoring ergonomics  — seeded from Resources
  - Scope: small fixes to navigation, structured data, and print styles; keep content and assets tidy.
  - Status: ongoing; incremental.

# Next ideas / backlog

- Share my Anki Chinese learning deck
  - Description: publish a cleaned subset of my Mandarin Anki cards (HSK‑oriented), plus a short guide on how I maintain the deck and schedule reviews.
  - Output: a post with download link and maintenance tips; possibly a GitHub repo release.

- Mock screenshot generator for Office apps (Word/PowerPoint)
  - Description: generate images that mimic editor chrome and a sample document layout from a simple text input (for presentations and docs without leaking real data).
  - Output: small CLI or web demo; template packs for Word and PowerPoint themes; write‑up on approach and constraints.

- Link checker CI for this site  — seeded from Resources
  - Description: lightweight GitHub Action to validate internal and external links on PRs.
  - Output: `ruby/setup-ruby` + `htmlproofer` pipeline with a short allowlist for known false positives.

- Diagram‑as‑Code cookbook  — seeded from Resources
  - Description: practical snippets for PlantUML, Mermaid, and Graphviz (themes, layout tricks, and export recipes) with copy‑paste ready examples.
  - Output: post series or a dedicated repo linked from the blog.

- Resolver safety and defaults in Office‑stamper
  - Description: consolidate findings on optional/nullability defaults and safe resolver patterns.
  - Output: Monthly Topic post with taxonomy, pitfalls, and a checklist; code samples in the library.

- Presentations‑as‑Code starter
  - Description: a thin starter showing Reveal.js or Marp with syntax‑highlighting presets and CI export to PDF.
  - Output: repo + short guide; link from the blog’s Resources.

# How I prioritize

I pick tasks that:

- unblock current users or reduce maintenance toil,
- can be shipped in small, verifiable increments,
- produce reusable checklists, scripts, or examples others can adopt.

If you depend on any of these, tell me — real users trump curiosity.
