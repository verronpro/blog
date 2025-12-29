---
layout: article
title: "Boring CI — Document Processing in Constrained Environments"
date: 2025-12-29
categories: [toolchain]
author: Joseph
tags: [ci-cd, agility, craftsmanship, docs-as-code, solo-maintainer, enterprise, windows, ubuntu]
description: "Why predictable, constraint-friendly CI beats sophisticated pipelines for solo maintainers and enterprise adopters."
---

# Thesis
Solo maintainers shouldn't mimic Google. Enterprise adopters work in restricted environments (no network, custom certs, strict JDKs). "Boring" CI—predictable, mainstream, and explicit—is the most effective way to communicate quality and ensure reproducibility.

# Technique: Predictable CI
- Use mainstream tools: Maven Surefire, Failsafe, and Asciidoctor plugins. Avoid exotic runners.
- Explicit configuration: define JDK versions, toolchains, and dependencies to remove environment assumptions.
- Cross-platform parity: test on both Windows and Ubuntu to catch path and encoding issues early.
- Docs as part of the build: render site in CI; fail on broken links or missing docs.
- No network dependencies: commit data samples (CSV, JSON, XML) to the repo for end-to-end CLI tests.

# Pitfalls
- Fancy pipeline trap: over-engineering CI with custom runners and complex strategies.
- Implicit environment: relying on "it works on my machine" instead of explicit toolchain config.
- Testing in a vacuum: ignoring the OS and network constraints of enterprise users.

# Solo-maintainer + Enterprise value
- CI as Runbook: documents exactly how the project is built and tested.
- Quality Gate: prevents regressions and maintains standards without manual intervention.
- Communication: shows enterprise users that the project understands and respects their constraints.

# Checklist
- [ ] Implement a basic matrix build on standard platforms (Linux + Windows).
- [ ] Automate documentation builds and link validation in the main pipeline.
- [ ] Use mainstream, LTS versions for all build tools and JDKs.
- [ ] Provide helpful, domain-specific logs for test failures.
- [ ] Commit minimal data fixtures for offline verification.
