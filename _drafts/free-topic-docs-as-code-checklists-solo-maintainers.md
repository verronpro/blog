---
layout: article
title: "Docs-as-Code Checklists for Solo Maintainers"
date: 2024-08-21
categories: [documentation-as-code]
author: Joseph
tags: [agility, craftsmanship, docs-as-code, solo-maintainer, enterprise, governance, checklists]
description: "Repeatable micro-rituals to keep documentation reliable and auditable in enterprise contexts."
---

# Thesis
Documentation is the highest-leverage artifact for solo maintainers. Checklists turn "good documentation" from a vague goal into repeatable steps that travel across PRs and releases, reducing support load and compressing onboarding.

# Core Checklists

## A. Change PR Checklist
- [ ] Update site pages (`engine/src/site/asciidoc/`) in the same PR.
- [ ] Adjust Javadoc for public API touchpoints; keep contracts and failure modes explicit.
- [ ] Provide one minimal (<15 lines), compilable example against public types.
- [ ] Validate AsciiDoc locally; fix broken anchors and maintain ~120 char line width.

## B. Release Checklist
- [ ] Update `RELEASE_NOTES.adoc` with value narrative and migration notes.
- [ ] Link the month's most impactful change to the exact GitHub commit.
- [ ] Run link check scripts against Javadoc and commit URLs.
- [ ] Verify examples still compile with the release version.

## C. Template Change Checklist
- [ ] Show before/after for templates using comments or SDTs.
- [ ] Remove control metadata in "after" views; use tight boundaries in examples.
- [ ] Prefer domain-aware APIs (`Paragraph.replace`) over low-level DOM surgery.

# Solo-maintainer + Enterprise value
- Scaling answers: self-teaching documentation reduces meeting load for the maintainer.
- Truthful state: ensuring docs provide a security/compliance-friendly picture of what's supported.
- Discipline: checklists prevent "docs debt" when under time pressure.

# Pitfalls
- Checklist fatigue: keep lists short and automate where possible (site build, link check).
- Duplication: link contracts (Javadoc) to workflows (site); don't copy.
- Stale examples: prefer small, compilable snippets; include from source where feasible.

# Checklist for Implementation
- [ ] Start with one PR checklist and one release checklist.
- [ ] Automate a docs gate in CI (build site + validate anchors).
- [ ] Make docs update part of the "Definition of Done" for every user-visible change.
