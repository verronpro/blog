---
layout: article
title: "How I Learned That Code Comments Aren't Enough (And Why I Started Writing Design Docs)"
date: 202X-XX-XX 09:00:00
categories: [ TODO ]
author: Joseph
tags: [ docs-as-code, agility, craftsmanship,
        solo-maintainer, enterprise, governance, adr, extensibility ]
description: "Why I stopped relying on commit messages and code comments to explain design decisions and started writing proper design documentation."
---

# The Solo Maintainer's Memory Problem

When you're a solo maintainer, you think you remember everything. You remember why you designed that API a certain way,
why you chose one approach over another, what you intended to support and what you didn't.

Then three months pass. Then six. Then a year.

Suddenly, you're looking at your own code wondering "What was I thinking?" and "Why did I do it this way instead of the
obvious alternative?"

# The Enterprise Adoption Reality Check

The problem got worse when enterprise teams started adopting office-stamper. They'd show up with questions:

- "Is this extension point stable?"
- "Can we rely on this behavior?"
- "What happens if we do this?"

I couldn't just say "trust me" - they needed documentation, stability guarantees, and clear contracts. These teams work
under security and architecture review constraints where undocumented behavior is risky behavior.

# My Documentation Tooling Evolution

Coming from experience with tools like Asciidoctor and Doxygen, I understood the value of living documentation. But I
needed something more focused than full system documentation - I needed a way to document specific design decisions.

That's when I discovered Architecture Decision Records (ADRs), but I quickly realized I needed to adapt them for the
solo maintainer context.

# Why I Ditched Commit Messages for Real Documentation

Early on, I tried to capture design decisions in commit messages and code comments. This didn't work because:

- Commit messages get buried in version control history
- Code comments become outdated when code changes
- Neither provides the context enterprise users need
- Both are terrible for future-me trying to remember what past-me was thinking

# My Lightweight Decision Documentation Approach

I created a simplified ADR approach that works for solo maintainers:

## Focus on Extension Points

I only document significant design decisions, especially those related to extension points. If users can extend
office-stamper through comment processors or resolvers, those APIs need clear documentation.

## Keep It Simple

Each decision record answers just a few key questions:

- What problem are we solving?
- What options did we consider?
- What did we choose and why?
- What's in scope and out of scope?
- What stability guarantees do we provide?

## Make It Living Documentation

Unlike traditional ADRs that are write-once documents, mine evolve with the code. When guarantees change or APIs evolve,
I update the corresponding decision records.

# The Solo Maintainer + Enterprise Sweet Spot

This approach solves several problems at once:

## Scaling Intent Across Time Zones

When enterprise teams touch the code quarterly, they can read decision records to understand the rationale behind APIs.
They don't need to schedule time with me - the documentation is self-service.

## Reducing Meeting Load

Procurement and platform reviewers appreciate traceability. Instead of scheduling meetings to explain design decisions,
I point them to documented decisions with rationale and alternatives considered.

## Supporting Sporadic Contributors

External contributors can understand not just what the code does, but why it does it that way. This helps them make
changes that align with the project's design philosophy.

# My Asciidoctor-Inspired Decision Documentation

Drawing from my experience with documentation tools, I treat decision records like any other documentation:

- Store them with the code in version control
- Link them from relevant Javadoc and user documentation
- Reference them in PRs and issues
- Keep them updated as decisions evolve

This approach mirrors how tools like Doxygen create living documentation that evolves with the codebase.

# Practical Implementation

If you're a solo maintainer dealing with enterprise users:

1. **Start small**: Document just your most important extension points
2. **Keep it focused**: One decision per document, a few paragraphs each
3. **Link it up**: Connect decision records to code and user documentation
4. **Make it evolve**: Update records when decisions change
5. **Use it**: Reference records in PR reviews and user support

# The Unexpected Transformation

Writing decision records changed how I design features. Knowing I'd have to explain my choices forced me to think more
carefully about tradeoffs. The documentation became a design tool, not just a record of decisions.

It also made me a better communicator with users. Instead of trying to explain complex design choices in GitHub
comments, I could point to well-written documentation.

# For Enterprise Users and Contributors

When you're working with a solo-maintained project:

- Check the decision records before designing your extensions
- Understand what's supported vs. what's accidental behavior
- Contribute back by helping improve documentation
- Be patient with the solo maintainer - good documentation takes time

The goal is to create a project that's self-service for users while sustainable for the maintainer. Decision records are
how I've achieved that balance.

Inspiration: Michael Nygard’s ADRs; "Decisions, not discussions" as a
team habit.
