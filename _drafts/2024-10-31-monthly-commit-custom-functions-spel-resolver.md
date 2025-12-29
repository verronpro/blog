---
layout: article
title: "Monthly Commit — Custom Functions and Suppliers in SpEL"
date: 2024-10-31
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management, spel, extensibility]
description: "Extending expressions safely with custom functions and suppliers in the SpEL resolver."
---

Commits:
- [e438ddc](https://github.com/verronpro/office-stamper/commit/e438ddc)
- [c7fbf1d](https://github.com/verronpro/office-stamper/commit/c7fbf1d)

# Why this is October’s highlight
- Support for registering custom functions and suppliers in SpEL.
- Goal: agility with safety; empower authors without creating security holes.

# What changed
- SpEL resolver extension surface for namespaced functions.
- Controlled registration via `DocxStamperConfiguration`.
- Updated documentation with registration/call examples.

# Agile and craftsmanship lens
- Pit of success: provide tiny, intention-revealing functions.
- Boundaries: keep complexity behind purpose-built functions.
- Functions as contract: versioned, tested, and documented.

# Solo-maintainer + enterprise usage angle
- Reduces support for obscure SpEL fragments.
- Governance win: security/platform teams review a single list of functions.

# Risks and mitigations
- Sprawl: use namespacing and pruning.
- Side effects: keep functions pure/deterministic.
- Security: use whitelisting; avoid reflection-based open resolvers.

# How to apply
- Start with high-value helpers (dates, money, safe wrappers).
- Document catalog in repo (name, signature, example, null-behavior).
- Add unit tests for SpEL invocation.
