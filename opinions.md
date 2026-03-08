---
layout: page
title: Opinions & Perspectives
permalink: /opinions.html
description: A diary of my evolving thoughts on software craftsmanship, solo maintainership, and the pragmatic trade-offs of building reliable systems.
aside:
  toc: true
---

This page serves as a living diary of my thoughts and ideas—a memory bank of the principles that guide my work. These opinions are shaped by years in the industry and my current journey as a solo maintainer and IT leader.

## On Solo Maintainership & Context
One of the most significant lessons from my work on **Office-stamper** is the importance of matching your approach to your reality. I initially treated it like a high-stakes "day job," but since moving to China and transitioning into an IT Manager/CISO role, I’ve realized it is a passion project. Every effort now targets small, iterative improvements and self-evident documentation. I no longer try to "harmonize" the repository with a non-existent company codebase; I am the solo maintainer, and lean, focused progress is the new gold standard.

## On Documentation & Scaffolding
I have a deep appreciation for **Asciidoctor** and the complex document generation it enables, but **Markdown** remains the undisputed winner for simplicity and ecosystem integration. My strategy is to use Markdown for the core and leverage **Pandoc** as a bridge for harder formats. I’ve found that "living documentation" tools—test reports, code scanners (like Doxygen), and diagram-as-code (PlantUML, Mermaid, Graphviz)—are exceptional scaffolding tools when you need to keep mental models in sync with reality.

## The Solo Quality Challenge
My most "controversial" stance remains the absolute importance of high-quality gatekeeping. I love **mob programming** for the sheer number of eyes it puts on code, which invariably enhances long-term velocity. Maintaining this mindset as a solo maintainer is a challenge. I compensate by building a **Virtual Team**: aggressive automated feedback loops (Qodana, PIT), strict test failures, and automated review tools (CodeQL, SonarQube, Renovate). I aim to react fast to sporadic contributors, treating their feedback as the "human" element of my mob.

## Stories That Shaped Me
Several moments define my craft:
- The days we spent purely on design solutions, refusing to touch the keyboard until the problem was solved.
- Building a test engine for electrotechnical engineers to bring software rigor to a domain unused to it.
- Safely refactoring an overconfident colleague's "untestable" code into a robust, integrated component.
- Discovering that "code beauty" can sometimes be secondary to anticipating edge cases, as I learned during an intense "extreme startup" session where pragmatic speed won the day.

## On Patterns & Evolution
Mob programming isn't a catch-all; design phases are often better suited for solo or pair brainstorming. Similarly, technology evolves: **Lombok** is largely redundant now that Java's primitives have matured. I’ve also moved away from strict "layering" for modern microservices—if an app is just a layer over an API or DB, scraping the boilerplate for automated controllers makes more sense. I now prefer generating controllers *from* documentation (contracts) rather than the other way around; the contract is the unit I test, not just the implementation.

## Rebuild Lean vs. Reuse Heavy
I generally prefer reuse over rebuilding, but I’ve learned to identify when "standard" tools become a source of friction. A prime example is my recent **AsciiDoc Pivot**. I chose to build a custom, zero-dependency Java parser for internal conversions rather than adopting the heavy JRuby-based AsciidoctorJ runtime. If a tool’s weight threatens the main goal, I will always choose to build a leaner, focused alternative that unblocks progress.
