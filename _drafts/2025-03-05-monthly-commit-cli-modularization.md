---
layout: article
title: "Monthly Commit — CLI Modularization"
date: 2025-03-05
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management]
description: "Decoupling the CLI from the core engine for better boundaries and independent releases."
---

Commit:
[fd98481](https://github.com/verronpro/office-stamper/commit/fd98481)

# Why this is March’s highlight
- Modularizes the CLI into its own independent module.
- Reduces coupling and enables separate release cadences.

# What changed
- Independent CLI module with its own `pom.xml`.
- Migrated argument parsing and terminal UX out of the engine.
- Cleaned up engine API to be format-agnostic and orchestratable.
- Separate build and assembly for CLI distributables.

# Agility and DX impact
- Independent iteration: enhance CLI without touching core engine.
- Better testing seams: engine (units/integration) vs. CLI (end-to-end).
- Clearer ownership for tooling/UX vs. engine specialists.

# Practical guidance
- Identify boundaries by change frequency and stakeholder.
- Expose minimal interfaces; push variability (flags, formats) to the interface module.
- Treat CLI output and exit codes as part of the contract.

# Risks and mitigations
- Duplication: use a small shared library or keep utilities in the engine.
- Drift: use checklists for new features (Engine + CLI + Docs).
- Packaging complexity: use Maven assembly; keep dependencies tight.
