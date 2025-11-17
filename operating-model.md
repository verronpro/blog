---
layout: page
title: Operating Model — Influences and Practices
permalink: /operating-model.html
aside:
  toc: true
---

This page organizes the ideas, practices, and sources that shape my work. It is written for my future partners looking for someone pragmatic to deliver results and leave systems healthier than they were found.

What you should take away: my influences are deliberate, complementary, and operational. They translate into repeatable ways of working that reduce delivery risk, make quality observable, and keep documentation, code, and architecture aligned.

Looking for the long, curated resource list? See the Resources page: [/resources.html](/resources.html).

## Core motivations

- Make complex work visible early and often. If we can see it, we can steer it.
- Prefer small, safe steps that ship value continuously.
- Treat docs, code, and diagrams as one artifact set. Reduce drift by generating and validating where possible.
- Leave teams with tools and habits they can sustain without me.

## Influences I stand on

These are not name‑drops; they shape concrete choices in how I work with you.

- Team Topologies — structure teams for fast flow and clear responsibilities.
- Docs‑as‑Code and Diagram‑as‑Code — text, versioned, reviewable, automated.
- BDD and example‑guided development — make intent executable.
- Conventional Commits and lightweight governance — predictable history, easier automation.
- Solo‑maintainer discipline — ruthless clarity on scope, automation, and quality gates.

## Practices I bring to your project

1) Docs‑as‑Code foundation
    - Your documentation, run‑books, and decisions live with your code in Markdown, with CI checks. Fewer stale wikis; more trustworthy knowledge.
    - Diagram‑as‑Code (PlantUML, Mermaid, Graphviz) keeps architecture current. Diagrams update when code or config changes.
2) Template and document automation
    - I maintain Office‑stamper, a small Java library used to generate .docx/.pptx/.xlsx from real templates safely and testably.
    - I apply strong template semantics (placeholders, invariants, pre/post‑processing) so documents are robust and evolvable.
3) Testing as infrastructure
   - Characterization tests make legacy safer to change.
   - CI pipelines validate docs, diagrams, and generated artifacts along with code.
4) Developer experience and maintainability
    - Git hygiene, commit conventions, minimal branching rules, and helpful automation.
    - Clear contribution pathways for occasional contributors.

## How it fits together (operating model)

Think of it as a small, reliable factory:

- Inputs: business intent, examples, existing assets, and constraints.
- Process: capture intent as text (docs), examples as tests, architecture as diagrams; automate generation and validation; iterate in small PRs.
- Outputs: working software or automations, repeatable documentation, accurate diagrams, and a CI pipeline that keeps them in sync.

You get rapid feedback, lower integration risk, and artifacts you can evolve without me.

## Engagement patterns

- Discovery and setup (1–2 weeks)
  - Map desired outcomes and constraints.
  - Stand up docs‑as‑code, diagram‑as‑code, and CI checks.
  - Baseline architecture and operational docs from what actually runs.

- Delivery cycles (2–6 weeks)
  - Prioritized increments with measurable checks.
  - Weekly demos with generated artifacts and diff‑based changes.

- Handover and enablement (1–2 weeks)
  - Maintainership checklist, contribution guide, and working examples.

## Proof points and open work

- Office‑stamper (Java) — source: https://github.com/verron.pro/office-stamper/
- Presentations‑as‑Code — post: /agility/craftsmanship/docs-as-code/solo-maintainer/enterprise/maintenance/platform/ci-cd/2024/01/01/presentations-as-code.html
- Monthly Commit series — see Archive for concrete improvements shipped in small steps.

## Selected tools and references I use

- Diagram‑as‑Code: PlantUML, Mermaid, Graphviz, Kroki
- Generators and docs: Jekyll (this site), Markdown, MathJax
- Testing: characterization tests, CI with simple Ruby/Java toolchains
- Governance: Conventional Commits, lightweight ADRs
- Team design: Team Topologies

## Where to find me

- Blog: https://blog.verron.pro/
- LinkedIn: https://www.linkedin.com/in/josephverron/ & https://www.linkedin.com/company/caring-coder
- GitHub: https://github.com/caring-coder & https://github.com/verronpro
- Medium: https://medium.com/@caring_coder

## Contact

If this operating model matches your needs, reach out via LinkedIn or the blog. I’m open to short, outcome‑focused engagements and happy to start with a small audit or a spike to prove value quickly.
