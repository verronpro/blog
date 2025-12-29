---
layout: article
title: "Living ADRs — Architecture Decision Records for Solo Maintainers"
date: 2025-12-29
categories: [documentation-as-code]
author: Joseph
tags: [docs-as-code, agility, craftsmanship, solo-maintainer, enterprise, governance, adr, extensibility]
description: "Why design documentation beats commit messages for scaling intent across time and teams."
---

# Thesis
Solo maintainers often forget their own design rationale, and enterprise users need documented stability guarantees. Lightweight, living ADRs (Architecture Decision Records) bridge the gap between "past-me" and external adopters.

# Technique: Lightweight ADRs
- Focus on extension points: document significant APIs (comment processors, resolvers).
- Keep it simple: answer What problem? Options? Choice & Why? Scope? Stability?
- Make it living: update records when APIs evolve; don't treat them as static artifacts.
- co-location: store records in version control with the code.

# Pitfalls
- Relying on memory: "I'll remember why I did this" is a fallacy after 6 months.
- Buried intent: commit messages are hard to find; code comments get stale.
- Undocumented risk: enterprise teams avoid "trust me" APIs without documented contracts.

# Solo-maintainer + Enterprise value
- Scaling intent: self-service documentation for teams working across time zones.
- Reducing meeting load: point procurement and platform reviewers to linkable rationale.
- Supporting sporadic contributors: help them align with the project's design philosophy.

# Checklist
- [ ] Start small: document the most critical extension points first.
- [ ] One decision per document; a few paragraphs each.
- [ ] Link decision records from relevant Javadoc and user docs.
- [ ] Use ADRs to think through tradeoffs during the design phase.
- [ ] Reference records in PR reviews and user support.
