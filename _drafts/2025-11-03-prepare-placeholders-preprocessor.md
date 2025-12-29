---
layout: article
title: "Monthly Commit — PreparePlaceholders Preprocessor"
date: 2025-11-03
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management]
description: "Introducing a normalization pass to make placeholders atomic and reduce downstream complexity."
---

Commit:
[822eff5](https://github.com/verronpro/office-stamper/commit/822eff5)

# Why this stands out
- Real-world templates are messy (placeholders split across runs, crossed ranges).
- `PreparePlaceholders` normalizes these regions before stamping.
- Pushes incidental complexity to the front, letting core processors focus on intent.

# What actually changed
- Introduced deterministic scan to make placeholders atomic (contiguous text).
- Detects and adjusts comment ranges that cross SDTs or split placeholders.
- Encapsulated cleanup heuristics behind an idempotent, fast contract.
- Co-located examples and tests; normalization is now a named configuration step.

# Agile and craftsmanship angles
- Shift quality left: normalization reduces edge cases downstream.
- Keep the contract small: preprocessors are easier to reason about than call-site fixups.
- Fail fast: precise messages help authors repair invalid templates early.

# Solo-maintainer + enterprise usage angle
- Reduces support tickets by eliminating "it works/it fails" flakiness.
- Easier audits: named, optional step with documented guarantees.

# Risks and mitigations
- Layout changes: scope transformations narrowly to placeholder regions.
- Hidden regressions: port characterization tests; expose metrics in debug runs.
- Performance: use linear scans and bounded look-ahead; profile large documents.

# How to apply
- Add deterministic, idempotent pre-normalization before core processing.
- Make placeholders atomic; avoid relying on coincidental run boundaries.
- Treat normalization results as a contract and assert invariants in tests.
