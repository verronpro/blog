---
layout: article
title: "Secure XML Parsing — Security by Default"
date: 2025-10-10
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management, security]
description: "Enabling secure processing by default in XML factories to mitigate XXE and other attacks."
---

Commit:
[0d18a0f](https://github.com/verronpro/office-stamper/commit/0d18a0f)

# Why this commit stands out
- In Office formats (DOCX/PPTX/XLSX), parser settings are security settings.
- Enables secure processing by default to mitigate XXE and billion-laughs attacks.
- Raises the security floor: callers don't need boilerplate to be safe.

# What actually changed
- Centralized XML parser creation behind a factory with secure defaults.
- Disabled external entity resolution and DTD processing where unnecessary.
- Consistent adoption of `XMLConstants.FEATURE_SECURE_PROCESSING`.
- Tests to assert locked-down behavior (bounded expansion, no external lookups).
- Documented threat model and defaults on the site.

# Agile/craftsmanship/docs-as-code lens
- Shallow pit of success: safe defaults beat remembering flags at call sites.
- Explicit factories: funneling creation reduces configuration drift.
- Docs as code: narrative updates alongside flag changes in the same PR.

# Solo-maintainer + enterprise usage angle
- Reduces incident risk and audit surprises.
- Aligns with enterprise policies: no outbound lookups, bounded resources.

# Risks and mitigations
- Breaking permissive behavior: document as intentional; provide escape hatch.
- False sense of security: enumerate protected vectors and point to tests.
- Divergence: forbid ad-hoc parser instantiation in code review.
