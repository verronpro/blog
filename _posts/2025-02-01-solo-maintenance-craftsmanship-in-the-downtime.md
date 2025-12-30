---
layout: article
title: "Solo Maintenance — Craftsmanship in the Downtime"
date: 2025-02-01
categories: [governance]
author: Joseph
tags: [agility, craftsmanship, docs-as-code, solo-maintainer, enterprise, process, quality]
description: "Sustainable practices for solo maintainers: embracing constraints, coffee-shop rituals, and the power of 'not yet'."
---

# Thesis
Solo maintenance isn't a day job—especially when your day job is managing IT in a different time zone. Success isn't measured by commit frequency but by sustained changeability, and the ability to pick up exactly where you left off, whether you're in a Beijing coffee shop or on a long commute. Embracing the constraints of limited time and unreliable networks isn't just a survival tactic; it's a forcing function for better design.

# Technique: Sustainable Rhythm

## The "Coffee Shop" Ritual
I no longer have the luxury of dedicated "craft days." Since moving to Beijing and transitioning to an IT Manager role, my "office" is often a laptop in a full-screen mode during a commute or while sitting next to my wife. The ritual is simple: if the IDE is full-screen and I have 60 minutes, I'm "on." 

To make this work, I've had to master two things:
- **Context Preservation**: Every session must end with a "clean" state, or a failing test that tells me exactly what to do next.
- **Impact Slicing**: Breaking work into increments so small they can be finished in a single sitting, yet so impactful they move the needle.

## Strategic "Not Yet"
Restraint isn't just about saying "no"; it's about "not yet." Take [Issue #80](https://github.com/verronpro/office-stamper/issues/80)—"Only attempt to resolve variable if they actually are shown." It has been pending since October 2022. I haven't ignored it; I've been waiting for the library's architecture to be ready to handle it without a bespoke hack. That delay allowed for a deeper conversation with the requester and a more sensible implementation strategy.

## The Portable Build (The "China Constraint")
Living in China as a digital nomad turned the "No Network" rule from a preference into a hard requirement. When Western platforms are throttled or blocked, you learn quickly that a build that requires a steady connection is a broken build. Making the project portable and network-independent didn't just help me work on the move; it made the project more robust for any remote CI worker.

# Pitfalls
- **The Velocity Trap**: Don't compare your solo project's pace to a corporate team. Your "pride" project doesn't put food on the plate, so don't let it consume your life.
- **Context Bleed**: Trying to tackle "grand refactorings" in 45-minute bursts. If you can't see the end of the change from the start of the session, don't start it.
- **Perfect as the Enemy of Done**: Sometimes you have to deliver a lower-quality increment to see the end result you envision. Refine it in the next "downtime" block.

# Solo-maintainer + Enterprise value
- **Predictability**: A stable, well-documented project is easier for enterprise teams to trust, even if the maintainer is "sporadic."
- **Scaling through Docs**: My documentation and tests are my "virtual teammates." They answer the questions I don't have time to answer in real-time meetings.
- **Resilience**: A build that works offline in a Beijing coffee shop will work anywhere.

# Checklist
- [ ] **Trigger Focus**: Find your "full-screen IDE" equivalent.
- [ ] **Build Portability**: Can you run your full test suite on a plane? If not, fix your dependencies.
- [ ] **Document the 'Why'**: Use ADRs or detailed issue comments to preserve the "why" for your future, time-crunched self.
- [ ] **Small Batches**: If a PR takes more than two "downtime sessions" to finish, it's too big.
- [ ] **Accept Imperfection**: A smaller, slightly imperfect increment that moves you forward is better than a perfect one that never gets committed.
