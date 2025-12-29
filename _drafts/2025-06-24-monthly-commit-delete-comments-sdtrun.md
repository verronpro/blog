---
layout: article
title: "Monthly Commit — Precise Comment Deletion in SdtRun"
date: 2025-06-24
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management]
description: "Introducing precise support for deleting comments attached to SdtRun elements."
---

Commit:
[498f37e](https://github.com/verronpro/office-stamper/commit/498f37e)

# Why this matters
- Comments used as control metadata (e.g., `displayIf`, `repeat`) can linger and cause issues.
- Tightens the pipeline invariant: processors should leave documents in a clean state.

# What changed
- Support for removing comments from `w:sdt`/`w:sdtContent` when represented as `SdtRun`.
- Utility methods to navigate and remove exact `w:commentRangeStart/End` and `w:commentReference`.
- Idempotent and safe removal across nested structures.

# Agility and craftsmanship angles
- Make correctness cheap: prevents "heisenbugs" later in the pipeline.
- Honor boundaries: enforces a clear lifecycle for control metadata.
- Reduce cognitive load: authors see only final content, not hidden scaffolding.

# Practical guidance
- Treat control metadata as ephemeral; remove it close to where it's consumed.
- Prefer specific, domain-aware helpers over generic DOM surgery.
- Write idempotent utilities for multi-processor pipelines.

# Risks and mitigations
- Over-removal: scope deletion to processor-owned ranges.
- Breaking nested structures: rely on `DocxIterator` and unit tests.
