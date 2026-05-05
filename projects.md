---
layout: page
title: Projects
permalink: /projects.html
description: A living list of ongoing work and projects I’m actively building — from docs-as-code tooling to Office automation experiments.
aside:
  toc: true
---

This page tracks current projects and initiatives I’m actively building, along with a focused backlog of work in progress. If you want to collaborate on any of these, or if a topic would help your team, reach out via LinkedIn (see About) or GitHub issues.

Tip: Items marked with “seeded from Resources” originate from the curated list on [/resources.html](/resources.html).

## Ongoing projects

- Office‑stamper (Java) — **Stable Core**
  - Scope: tighten template semantics (placeholders, invariants, pre/post‑processing).
  - Status: **Shipped 3.1 & 3.2**. Resettable iterators are now live, improving code readability. Pre-processing and post-processing are equally shipped, enhancing context management for nested scope resolutions.

- Docs‑as‑Code pipelines for diagrams — seeded from Resources.
  - Scope: standardize PlantUML/Mermaid/Graphviz flows via Kroki; ensure diagrams build in CI alongside docs.
  - Status: ongoing; looking to publish a minimal starter and a checklist.

- Site hygiene and authoring ergonomics — seeded from Resources
  - Scope: small fixes to navigation, structured data, and print styles; keep content and assets tidy.
  - Status: ongoing; incremental.

# Next ideas / backlog

- Anki inspired app for Chinese learners
  - Description: Partnering with a local enthusiast in China to develop a dedicated app for Chinese learners that integrates and streamlines the learning process. (Replacing the previous idea of sharing a static HSK deck).
  - Output: Functional app and write-up on the development journey.

- Mock screenshot generator for Office apps (Word/PowerPoint)
  - Description: generate images that mimic editor chrome, and a sample document layout from a simple text input.
  - Status: **Prototype phase**. Using JavaFX as the graphical engine and the Office-stamper AsciiDoc module to compile formatted text to a JavaFX Scene. Rendering is functional but still needs refinement for clean, usable outputs.

- Link checker CI for this site — seeded from Resources
  - Description: lightweight GitHub Action to validate internal and external links on PRs.
  - Output: `ruby/setup-ruby` + `htmlproofer` pipeline with a short allowlist for known false positives.

- Diagram‑as‑Code cookbook — seeded from Resources
  - Description: practical snippets for PlantUML, Mermaid, and Graphviz (themes, layout tricks, and export recipes) with copy‑paste ready examples.
  - Output: post series, or a dedicated repo linked from the blog.

- Presentations‑as‑Code starter
  - Description: a thin starter showing Reveal.js or Marp with syntax‑highlighting presets and CI export to PDF.
  - Output: repo and short guide; link from the blog's Resources.

## How I prioritize

I pick tasks that:

- unblock current users or reduce maintenance toil,
- can be shipped in small, verifiable increments,
- produce reusable checklists, scripts, or examples others can adopt.

If you depend on any of these, tell me — real users trump curiosity.
