---
layout: article
title: "Monthly Commit — Consolidate WmlUtils"
date: 2025-11-02
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management]
description: "Reducing maintenance tax by consolidating run and paragraph helpers into a unified WmlUtils class."
---

Commit:
[d832b84](https://github.com/verronpro/office-stamper/commit/d832b84)

# Why this refactor matters
- Utility sprawl (near-duplicate helpers) creates a discovery tax.
- Consolidation improves cohesion: related behavior lives together with consistent naming.
- Reduces change size: targeting one module with one vocabulary instead of multiple classes.

# What actually changed
- Consolidated helpers into `WmlUtils` and removed `RunUtil`.
- Standardized method names and pre/postconditions.
- Aligned iterator helpers with `DocxIterator`/`TraversalUtil` patterns.
- Updated call sites and Javadoc for explicit contracts.

# Agile and craftsmanship angles
- One noun, one meaning: centralizing behavior makes reviews crisper.
- Lower WIP: developers touch fewer files per change.
- Docs as code: single source for examples and Javadoc links.

# Solo-maintainer + enterprise usage angle
- Sustainable speed: fewer names to break, fewer docs to update.
- Easier audits: enterprise reviewers follow one utility module with clear contracts.

# Risks and mitigations
- Hidden behavior changes: use characterization tests during consolidation.
- Kitchen sink anti-pattern: keep a high bar for inclusion in `WmlUtils`.
- Naming churn: provide mapping tables in docs and use clear deprecations.

# How to apply
- Map hotspots where similar helpers have drifted and consolidate.
- Document the utility catalogue (traversal, surgery, comments) on the site.
- Treat utilities as a public contract with stable examples.
