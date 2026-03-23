---
layout: article
title: Feedback Loops and the AsciiDoc Pivot
date: 2026-02-01
categories: [ office-stamper ]
tags: [ office-stamper, java, ci-cd, docs-as-code, refactoring ]
author: Joseph
description: Introducing Qodana and PIT for tighter quality gates, and a dramatic pivot to a lightweight, zero-dependency AsciiDoc parser.
---

Following the stabilization of the public surface in version 3.1, my focus for
January turned toward the "engine of the engine." As a solo maintainer, I don't
have a team to review my PRs or catch my blind spots. To maintain a
professional-grade codebase, I must rely on a "Virtual Team" of automated
feedback loops.

This month was defined by two major themes: hardening our quality gates, and a
fundamental pivot in how we handle test documentation.

## The Virtual Team: Qodana and PIT as Gatekeepers

While **Qodana** and **PIT** have been part of our repository for some time,
this month they graduated from "informative" to "enforcement" in our CI
pipeline. As a solo maintainer, I don't have a team to review my PRs or catch my
blind spots, so I must rely on a "Virtual Team" of automated feedback loops that
actually block merging when quality drops.

Qodana (by JetBrains) has been our static analysis tool, but this month I
updated the configuration to fail the build on new code smells and complexity
spikes. It now acts as my static analysis lead, providing a consistent baseline
for "clean code." I've also updated our GitHub repository to feature the
official Qodana badge, replacing the generic "analyze" workflow status.

Similarly, PIT (Mutation Testing) has been our quality auditor, but I’ve now
tightened its integration into the main `mvn verify` cycle. As
I [discussed previously]({% post_url
2025-06-01-monthly-topic-mutation-testing-the-truth-beyond-coverage %}), line
coverage is often an illusion. By making PIT results a first-class citizen in
our CI reports, I’ve been able to identify and fix several "dark corners" in our
`utils` module where code was executed but not truly verified.

## The AsciiDoc Pivot: Zero Dependencies

The most significant architectural change this month happened in the `asciidoc`
module. In late December, I had [planned to adopt AsciidoctorJ]({% post_url
2025-12-22-monthly-commit-the-high-fidelity-utils-module %}) to handle our
internal doc-to-text conversions.

However, by mid-January, the "JRuby tax" became unbearable. The overhead of the
JRuby runtime and the AsciidoctorJ wrapper increased our build times and
introduced a heavy dependency chain that felt out of place for a "lightweight"
library.

At the same time, I've been following exciting work by the
**Eclipse Foundation** on
the [AsciiDoc Language project](https://projects.eclipse.org/projects/asciidoc.asciidoc-lang),
working toward a formal specification and a native Java implementation. While we
wait for that standard to mature, I decided to pivot.

I've replaced the entire AsciidoctorJ integration with a **custom, lightweight
Java parser**. This was also a perfect opportunity to experiment with **GenAI**
to kickstart a side-project: I used AI to generate the first draft of the
parser, which I then refined to fit our specific needs.

### Why a Custom Parser?

1. **Performance**: Startup time for the conversion pipeline is now
   instantaneous, removing the definitely too slow JRuby.
2. **Future-Proofing**: By keeping our requirements minimal today, we'll be
   better positioned to adopt the official Eclipse AsciiDoc implementation once
   it is ready.
3. **Zero External Runtime**: No more external binaries or heavy runtimes
   required.

This pivot aligns with my core philosophy: use the right tool for the job, but
don't be afraid to build a simpler one if the "standard" tool is overkill,
especially when it serves as a bridge to a better future standard.

## Site Hygiene: Navigation and Logos

With the 3.1 release, our documentation site structure needed to evolve. I've
overhauled the site navigation to make the API guides and migration notes more
discoverable. You'll also notice a refined logo and color palette that matches
the professional aspirations of the project.

## Impact: Faster, Leaner, Safer

The result of this month's work is a codebase that is not only faster to build
but significantly safer to refactor. The "Virtual Team" handles the gatekeeping,
allowing me to focus on the next major milestone: version 3.2.

## Next Steps

With the quality gates in place, I'm now working on expanding the API surface to
support complex table manipulation—a feature that will land in the 3.2 release.

---

### February 1st 2026 — Commit Summary:

- **Feat**: Integrated **Qodana** for advanced static analysis in CI.
- **Feat**: Added **PIT** mutation testing to ensure test suite effectiveness.
- **Refactor**: Replaced AsciidoctorJ with a **custom lightweight AsciiDoc
  parser** in the `asciidoc` module.
- **Docs**: Restructured site navigation and updated branding/logos.
- **Refactor**: Cleaned up `utils` module (replaced legacy `instanceof` checks
  and refined lambda naming).
