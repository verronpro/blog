---
layout: article
title: "Java 25 Migration — Platform Alignment"
date: 2025-10-08
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management]
description: "Upgrading the Office-stamper baseline from Java 21 to 25."
---

Commit:
[d0495ba](https://github.com/verronpro/office-stamper/commit/d0495ba)

# Why this upgrade matters
- Keeps the project current with the ecosystem and enterprise security baselines.
- Reduces support load for adopters on current JDKs.
- Predictable upgrade cadence is part of the product.

# What changed
- Bumped Java baseline from 21 to 25 in Maven and CI.
- Aligned plugin versions, compiler flags, and matrix builds.
- Verified suite against multiple JDK vendors.
- Updated documentation for the new baseline.

# Agile and craftsmanship angles
- visible and incremental platform work (small PRs).
- Tests and static analysis as guardrails for wide changes.
- Boring choices: mainstream JDK and plugins reduce surprises.

# Solo maintainer + enterprise usage angle
- Reduces back-and-forth with enterprise platform/security teams.
- Faster triage: debugging on the same baseline as reporters.

# Risks and mitigations
- Runtime behavior changes: run suite against multiple vendors; centralize XML factory.
- Dependency drift: upgrade plugins in lockstep; keep surface area small.
- Downstream friction: document minimum runtime and provide clear error messages.
