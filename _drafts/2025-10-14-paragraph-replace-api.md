---
layout: article
title: "Paragraph Replace API — Quick Summary"
date: 2025-10-14
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management]
description: "Brief overview of the Paragraph.replace implementation."
---

Commit:
[a54e7b1](https://github.com/verronpro/office-stamper/commit/a54e7b1)

# Why it is interesting
- Text surgery in Word paragraphs is complex due to runs and interleaving.
- Makes text surgery a first-class, testable API.

# What changed
- Introduced `Paragraph.replace` and implemented for `StandardParagraph`.
- Centralized range calculations and validation.

# Craftsmanship angle
- Encapsulate complexity behind well-named methods.
- Push correctness checks to the boundary.
- Target a single API in tests for better confidence.
