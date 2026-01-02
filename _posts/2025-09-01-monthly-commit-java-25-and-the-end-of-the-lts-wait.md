---
layout: article
title: "Java 25 and the End of the LTS Wait"
date: 2025-09-01
categories: [ office-stamper ]
tags: [ office-stamper, java, ci, renovate, craftsmanship, maintenance ]
author: Joseph
description: Transitioning office-stamper to a "rolling" Java baseline to stay at the state of the art and eliminate migration debt.
---

In August 2025, `office-stamper` hit a significant milestone: we are no longer waiting for the next Long-Term Support (LTS) release to move the platform forward. 

For a long time, my strategy as a solo maintainer was the "safe" path: anchor the library to the latest LTS (Java 21) and stay there until the next "Big Bang" migration. But "safe" often means accumulating technical debt. By the time the next LTS arrives, the jump is massive, the breaking changes are numerous, and the effort required is exhausting.

This month, I decided to break that cycle. `office-stamper` is now baseline Java 25.

## Context: The State of the Art

The modern Java release cadence has proven itself. The releases are stable, the regressions are rare, and the features arrive incrementally. 

As a solo maintainer, I've realized that staying at the *state of the art* is actually easier than lagging behind. If I update to every Java version as it arrives, the migration "Big Bang" is replaced by a series of small, manageable "pops." 

## Change Summary

- **Java Baseline**: Bumped from Java 21 to Java 25 across all modules.
- **CI Modernization**: Updated GitHub Actions to `v5` (via Renovate) to ensure the pipeline uses the latest runner environments and security patches.
- **Dependency Refresh**: Aligned Spring Expression and Context to `7.0.2` and JUnit to `6.0.0` to match the new language baseline.
- **Documentation**: Overhauled Javadocs to use Markdown style, improving readability and AI-assisted "vibe coding" accuracy.

## Confidence through Automation

The engine behind this confidence is **Renovate**. Having notifications on the latest updates delivered directly to my inbox makes it possible to have frequent releases, even when they only include dependency bumps.

```yaml
# .github/workflows/integrate-os.yml
- uses: actions/checkout@v5
  with:
    fetch-depth: 0

- uses: actions/setup-java@v5
  with:
    java-version: 25
    distribution: 'temurin'
```

By keeping the CI pipeline modernized, I can trust the test suite to catch the edge cases. I don't have the time to manually ensure every new release stays usable with legacy libraries, but I *do* maintain a rigorous test matrix against various `docx4j` versions. This gives users a clear map: they can find at least one version of `office-stamper` that caters to their specific spot in the tech stack timeline.

## Impact

1.  **Zero Migration Debt**: We are already running on the latest platform. There is no "upcoming project" to upgrade Java; the work is done.
2.  **User Choice**: By releasing frequently, I provide a continuous stream of compatibility points. If a user is stuck on an older stack, they have a stable release to use. If they are moving forward, we are already there waiting for them.
3.  **Maintainer Sanity**: Debugging is faster when I'm running the same baseline as the latest reports. I'm not fighting "fixed-in-newer-version" ghosts.

## Next Steps

With the Java 25 baseline established, I'm turning my attention back to the core abstractions. The next focus is **StandardRun**—a refactor designed to hide even more of the `docx4j` internals behind our own domain-driven types.

---

### September 1st 2025 — Commit Summary:
- **Chore**: Upgraded Java baseline to 25.
- **CI**: Bumped `actions/checkout` and `actions/setup-java` to `v5`.
- **Deps**: Upgraded `spring-expression` and `spring-context` to `7.0.2`.
- **Docs**: Markdown-style Javadoc conversion complete.
- **Chore**: Introduced `.aiignore` to focus AI assistants on relevant source code.
