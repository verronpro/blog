---
layout: article
title: "CLI Multi-format Support — CSV, JSON, XML"
date: 2025-10-10
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management]
description: "Teaching the CLI to process common data formats for easier enterprise integration."
---

Commit:
[aa33652](https://github.com/verronpro/office-stamper/commit/aa33652)

# Why this commit stands out
- Meet users where their data is: CSV, JSON, XML/HTML, and Java properties.
- Pragmatic agility: reduce integration friction for enterprise adopters.
- Leverage with safety: format adapters at the boundary, stable engine core.

# What actually changed
- Extended CLI input adapters to parse multiple formats.
- Unified stamping flow to prevent format leakage into downstream logic.
- Improved CLI help with copy-pastable examples for each format.
- Updated docs with format-specific guides.

# Agile/craftsmanship/docs-as-code lens
- Optimize for first value: accept existing data to compress onboarding.
- Keep engine pure: adapters live at the edges, domain remains format-agnostic.
- Docs as code: examples ship with code and align with `--help`.

# Solo-maintainer + enterprise usage angle
- Removes reasons for users to fork or wrap the tool for data conversion.
- Reduced support: point to stable examples instead of writing custom scripts.

# Risks and mitigations
- Adapter sprawl: keep adapters small, tested, and documented.
- Divergent errors: normalize messages and exit codes.
- Performance: stream where applicable; document limits.
