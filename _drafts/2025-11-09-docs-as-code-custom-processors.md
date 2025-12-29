---
layout: article
title: "Docs-as-Code — Custom Processors Guide"
date: 2025-11-09
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management]
description: "Shipping a versioned guide for extending Office-stamper with custom processors and resolvers."
---

Commit:
[04e2de1](https://github.com/verronpro/office-stamper/commit/04e2de1)

# Why this commit stands out
- Agility is enabling others to move without you; extension guides empower teams to customize.
- Comprehensive guide for `CommentProcessor` and `ObjectResolver` (core extension seams).
- Docs-as-code: guide lives next to implementation and evolves in the same PR.

# What actually changed
- Detailed tutorial for `CommentProcessor`, `ObjectResolver`, and `ExpressionResolver`.
- Documented wiring components (`DocxStamperConfiguration`) for concept-to-code navigation.
- Included copy-pastable snippets that compile against public APIs.
- Guide integrated into Maven/Asciidoctor pipeline to catch link rot early.

# Craftsmanship and Agile impact
- Build the pit of success: reduce customization time to <30 minutes with minimal templates.
- Intent-revealing APIs: guide uses domain-friendly method names (`highlightIf`, `resolveImage`).
- Executable knowledge: snippets break when APIs drift, ensuring docs stay current.

# Solo-maintainer + enterprise usage angle
- Scale by artifact: turn repeated answers into linkable documents.
- Risk reduction: clarifies supported vs. internal APIs for platform and compliance reviewers.
- Synchronized schedule: teams can implement custom logic without waiting for the maintainer.

# Risks and mitigations
- Duplication: link Javadoc (contracts) to the site (workflows/examples); don't copy.
- Snippet rot: keep examples small; include from real source files where possible.
- Visibility: codify supported APIs in package boundaries and documentation.

# How to apply
- Co-locate docs and code; update both in the same PR as part of Definition of Done.
- Provide a "golden path" example per extension point.
- Automate docs in CI: build, validate links, and compile snippets.
