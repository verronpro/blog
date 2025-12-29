---
layout: article
title: "Monthly Commit — Smart Tag Run Factory"
date: 2025-11-03
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management]
description: "Introducing a focused factory for creating Smart Tags in WordprocessingML."
---

Commit:
[21e9451](https://github.com/verronpro/office-stamper/commit/21e9451)

# Why this commit stands out
- Constructing Smart Tags directly in WordprocessingML is verbose and error-prone.
- Introduces `newSmartTag` factory to make creation safe and repeatable.
- Provides leverage for occasional contributors through intention-revealing APIs.

# What actually changed
- Focused factory to build Smart Tag runs with sensible defaults.
- Centralized configuration to prevent duplication of low-level wiring.
- Documented pre/postconditions and usage examples.
- Aligned naming with existing paragraph/run abstractions.

# Agile and craftsmanship angles
- One noun, one meaning: replaces scattered raw construction sequences.
- Expressive seams: call sites declare intent; factory guards invariants.
- Docs as code: examples co-located with implementation and site docs.

# Solo-maintainer + enterprise usage angle
- Repeatability: reduces support questions for complex Word features.
- Self-service: engineers in large organizations can copy documented snippets.
- Auditable: single construction path with explicit defaults and edge-case handling.

# Risks and mitigations
- Kitchen sink factory: keep it tiny; use overloads instead of boolean flags.
- Default drift: document attributes and child run policy; use characterization tests.
- Library divergence: cross-link docx4j types and verify against library samples.

# How to apply
- Wrap verbose third-party constructs in small factories named after domain concepts.
- Assert factory behavior with realistic tests (XML or structure).
- Document preconditions and postconditions as part of the public contract.
