---
layout: article
title: "Monthly Commit — Paragraph Replace API"
date: 2025-10-14
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management]
description: "Encapsulating gnarly WordprocessingML text replacement behind a stable Paragraph.replace API."
---

Commit:
[a54e7b1](https://github.com/verronpro/office-stamper/commit/a54e7b1)

# Why this commit is the month’s standout
- Replacing text in Word paragraphs is tricky (split runs, styles, comments).
- Introduces `Paragraph.replace` to centralize surgery and invariants.

# What changed
- Added `replace` to `Paragraph` abstraction (implemented in `StandardParagraph`).
- Centralized range calculations and validations.
- Simplified call sites across processors by replacing multi-step manipulations.

# Agility and developer experience
- Smaller batch size: features requiring replacement touch fewer classes.
- Safer refactors: correctness lives in one place.
- Clearer reviews: `Paragraph.replace(from, to, replacement)` reads as intent.

# Practical guidance
- Encapsulate fragile mutation sequences behind intention-revealing APIs.
- Add precondition checks for fast feedback.
- Target the API boundary in tests, not just internal helpers.

# Risks and mitigations
- Over-fitting: keep `replace` minimal and composable (no side effects).
- Performance: micro-benchmark to avoid quadratic behavior in splitting runs.
