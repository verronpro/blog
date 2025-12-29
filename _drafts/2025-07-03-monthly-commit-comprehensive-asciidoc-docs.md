---
layout: article
title: "Monthly Commit — Comprehensive AsciiDoc Documentation"
date: 2025-07-03
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management, docs-as-code]
description: "Adopting AsciiDoc for a teachable platform and documentation-as-code."
---

Commit:
[ba78c33](https://github.com/verronpro/office-stamper/commit/ba78c33)

# Why this is July’s most interesting change
- Transforms Office-stamper into a teachable platform with comprehensive docs.
- Docs as code: content co-located with code and integrated into the build.

# What changed
- New AsciiDoc documentation site with coherent topic map.
- Documented core concepts, configuration, CLI, and extension points.
- Copy-pastable examples compiled against public API types.
- Maven integration for consistent site generation in CI.

# Agility and DX impact
- Faster onboarding through task-oriented guides.
- Shorter PR cycles: rationale lives next to the code under review.
- Safer refactors: build catches broken links and inconsistent snippets.

# Documentation-as-code patterns
- Co-location: keep docs in the same repo/PR as code changes.
- Small, runnable snippets: prefer examples readers can paste into tests.
- Single source: link between Javadoc and site to eliminate duplication.
- CI safety net: build site and check links in every PR.

# Risks and mitigations
- Documentation rot: make updates part of the Definition of Done.
- Sprawl: maintain task-oriented TOC; prune stale pages.
- Uneven voice: use a style guide and lint with AsciiDoctor presets.
