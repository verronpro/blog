---
layout: article
title: "Monthly Commit — StandardRun Refactor"
date: 2025-11-03
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management]
description: "Wrapping docx4j's raw Run in a project-owned StandardRun abstraction for better invariants."
---

Commit:
[dc47832](https://github.com/verronpro/office-stamper/commit/dc47832)

# Why this refactor stands out
- Replaces third-party primitives with project-owned abstractions to house invariants.
- `StandardRun` centralizes helpers (merge/split, normalize) and removes docx4j specifics from call sites.
- Provides a stable API that protects against regressions and simplifies onboarding.

# What actually changed
- Replaced raw `Run` manipulations with `StandardRun` in text-handling paths.
- Centralized behavior (split, merge, insert, replace, preserve formatting) behind a controlled API.
- Clarified contracts in Javadoc regarding whitespace and normalization.
- Updated tests to target the new abstraction boundary.

# Agile and craftsmanship angles
- Encapsulation: cursor math and surgery belong behind a type owning the contract.
- Delete to accelerate: smaller diffs focus on intent, leading to faster reviews.
- Docs as code: single place for behavior documentation and Javadoc.

# Solo-maintainer + enterprise usage angle
- Force multiplier: reduces surface area for mistakes and provides a focal point for bug fixes.
- reproducible: edge cases can be targeted with tiny fixtures against the abstraction.

# Risks and mitigations
- Behavior drift: port characterization tests; document formatting policies explicitly.
- Kitchen sink wrapper: keep surface area small; use adjacent utilities for rare cases.
- Performance: avoid quadratic merges in large paragraphs.

# How to apply
- Wrap third-party classes in thin domain wrappers for invariants and helpers.
- Use preconditions and clear failure messages at the boundary.
- Back the wrapper with micro-tests and realistic fixtures linked from docs.
