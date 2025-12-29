---
layout: article
title: "Monthly Commit — TraversalUtil Integration"
date: 2024-11-04
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management]
description: "Replacing custom DocumentWalker with docx4j's TraversalUtil for DOM traversal."
---

Commit:
[c3f2179](https://github.com/verronpro/office-stamper/commit/c3f2179)

# Why this is November’s standout
- Retires home-grown `DocumentWalker` in favor of docx4j’s `TraversalUtil`.
- Reduces accidental complexity and adopts well-tested library primitives.

# What changed
- Replaced internal walker with `TraversalUtil`.
- Simplified call sites using visitors expressing "what to visit" instead of "how to walk".
- Aligned internal utilities with `TraversalUtil` idioms.

# Agility and craftsmanship angles
- Delete to accelerate: removing bespoke infrastructure reduces surface area.
- Converge on common tools: lower onboarding costs for docx4j-aware developers.

# Practical guidance
- Prefer visitors/filters over manual walking.
- Keep traversal and mutation separate.
- Write characterization tests for tricky document structures.

# Solo-maintainer + enterprise usage angle
- Lowers onboarding time for occasional contributors.
- Simplifies audits; security/platform teams can assess based on known library semantics.

# Risks and mitigations
- Missing behaviors: use characterization tests.
- Performance: benchmark and specialize if needed.
