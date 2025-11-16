---
layout: article
title:  "Leveling Up the Toolchain: Java 17, JUnit 5, and a Dev Container"
date:   2023-03-01 09:00:00
categories: [office-stamper]
author: Joseph
tags: [office-stamper,toolchain, java, ci, devex, ci-cd, java17, junit5, devcontainers, agility, craftsmanship, docs-as-code, solo-maintainer, enterprise, maintenance, platform, maven]
description: "Java 17 + JUnit 5 migration with a ready‑to‑code dev container—predictable builds, modern tests, and faster onboarding for enterprise adopters."
---

TL;DR: Migrating to Java 17 LTS, JUnit 5, and a dev container turned maintenance overhead into competitive advantage—giving enterprise adopters predictable builds, modern tooling, and zero-friction onboarding in a solo-maintained project.

**Commits:**
- Java 17: [49b6c09](https://github.com/verronpro/office-stamper/commit/49b6c09)
- JUnit 5: [428d5d5](https://github.com/verronpro/office-stamper/commit/428d5d5)
- Devcontainer docs: [2f53681](https://github.com/verronpro/office-stamper/commit/2f53681)

## Why this stands out

Upgrading the foundation of a project is rarely glamorous, but it's exactly what keeps a small codebase alive and useful to large organizations over time. The project moved to Java 17 (last LTS), ported its tests to JUnit 5, and documented a ready‑to‑code dev container. The combination matters: a modern runtime unlocks language and performance improvements; a modern test stack streamlines developer experience; and a containerized setup makes sporadic contributors effective within minutes instead of hours.

For a solo‑maintained project used in big‑company projects, those are not "nice to have" tweaks—they are survival tools. Enterprises pin on LTS JDKs and require predictable builds; contractors rotate in and out; security scanners and compliance checks look for sane defaults. Shipping these changes in one focused epoch reduces friction for everyone who touches the code or the CLI.

## What the commits reveal

### Java 17: Future-Proofing Without Breaking Production (commit [49b6c09](https://github.com/verronpro/office-stamper/commit/49b6c09))

**Problem:** Java 11 reaches EOL; enterprises standardize on LTS versions.  
**Solution:** Unified upgrade across build, CI, and docs.

**Key moves:**
- CI consistency: All workflows → Temurin 17
- Language unlock: Compiler target 1.8 → 17 (enables records, pattern matching)
- Cleaner builds: Removed reflection workaround flags
- Dependency leap: Spring 5 → 6 (security + perf)

**Impact:** No breaking changes for end users; dev environment matches enterprise standards.

### JUnit 5 migration (commit [428d5d5](https://github.com/verronpro/office-stamper/commit/428d5d5))

This large commit completed the transition from JUnit 4 to JUnit 5 across the entire test suite (50 files changed):

- **Dependency swap:** Replaced `junit:junit:4.13.2` with `org.junit.jupiter:junit-jupiter:5.9.2`, the modern JUnit platform.
- **Annotation migration:** Converted `@Test`, `Assert.*` to JUnit 5's `org.junit.jupiter.api.Test` and static assertion imports (`assertEquals`, `assertNotNull`, etc.), making tests more expressive and IDE-friendly.
- **Code cleanup:** Reformatted test classes with consistent indentation (tabs), removed unused proxy builder stub (`ProxyBuilder.java`), and fixed typos in test names (`TabsIndendationTest` → `TabsIndentationTest`).
- **API improvements:** Updated internal utilities like `ExpressionResolver` and `CommentUtil` to return `Optional` instead of null, improving null-safety and aligning with modern Java idioms.
- **Test structure refinement:** Simplified test assertions and removed scattered `Assert.` prefixes in favor of static imports, reducing visual noise and making test intent clearer.

The migration wasn't just find-and-replace; it involved rethinking test patterns (optional returns, cleaner assertions) and establishing a consistent style that future contributors can follow without re-learning old conventions.

### Dev Container: From "Works on My Machine" to "Works Everywhere" (commit [2f53681](https://github.com/verronpro/office-stamper/commit/2f53681))

The contributor journey:

*Without devcontainer:*
1. Install JDK 17 (which vendor?)
2. Install Maven (which version?)
3. Configure IDE
4. Debug PATH issues
5. ~2 hours later: Run first test

*With devcontainer:*
1. Click "Reopen in Container"
2. 5 minutes later: Run `mvn test ✅`

*The file:* 27 lines that encode the entire environment contract. Works in VS Code, Codespaces, or any devcontainer-compatible tool.

*Enterprise benefit:* Security teams can audit the environment spec in one file instead of reverse-engineering setup docs.

## Agile/craftsmanship/docs-as-code lens

- **Small, verifiable changes:** Grouping platform upgrades behind green CI reduces the solo maintainer's cognitive load and limits the blast radius of issues.
- **Clarity over cleverness:** JUnit 5's parameterized tests and extensions make intent visible. When tests read like documentation, they *are* documentation-as-code.
- **Eliminate setup tax:** The dev container is executable onboarding. It encodes the environment contract so a contributor—or future you—doesn't rediscover dependencies on every machine.

> **💭 Maintainer Note:**  
> As a solo maintainer, every hour spent helping someone set up their environment is an hour *not* spent fixing bugs or adding features. The dev container isn't laziness—it's survival. When a contractor opens a PR and their first message is 'tests pass locally,' not 'how do I install Maven?', that's a win.

## Design Decisions

**Decision: Java 17 as minimum runtime**  
*Trade-off:* Excludes Java 8/11 users.  
*Rationale:* LTS support, security patches, modern APIs. Users stuck on older JDKs can use office-stamper 1.x.  
*Safety net:* Full test suite on Temurin, Corretto, and Azul Zulu.

**Decision: Devcontainer as optional**  
*Trade-off:* Not everyone uses VS Code.  
*Rationale:* Plain Maven remains primary; devcontainer is a productivity boost, not a requirement.  
*Safety net:* CONTRIBUTING.md documents both paths.

## Your Platform Upgrade Checklist
Planning a similar migration? Use this:
- *Audit dependencies* for LTS compatibility
- *Pin versions* in CI (JDK vendor + version)
- *Migrate tests incrementally* (one package at a time)
- *Run full suite* on multiple JDK vendors (Temurin, Corretto, OpenJDK)
- *Document runtime requirements* separately from dev requirements
- *Provide executable environment* (devcontainer, Dockerfile, or Nix flake)
- *Tag a release* immediately after green CI (makes rollback trivial)

## References

- **Commits:** [49b6c09](https://github.com/verronpro/office-stamper/commit/49b6c09) (Java 17), [428d5d5](https://github.com/verronpro/office-stamper/commit/428d5d5) (JUnit 5), [2f53681](https://github.com/verronpro/office-stamper/commit/2f53681) (devcontainer)
- **Build:** Maven + Surefire/JUnit 5
- **Docs:** CONTRIBUTING and devcontainer documentation
