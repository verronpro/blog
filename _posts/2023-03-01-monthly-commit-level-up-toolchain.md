---
layout: article
title:  "Leveling Up the Toolchain: Java 17, JUnit 5, and a Dev Container"
date:   2023-03-01 09:00:00
categories: [agility, craftsmanship, docs-as-code, solo-maintainer, enterprise, maintenance, platform, ci-cd]
---

**Commits:**
- Java 17: [49b6c09](https://github.com/verronpro/office-stamper/commit/49b6c09)
- JUnit 5: [428d5d5](https://github.com/verronpro/office-stamper/commit/428d5d5)
- Devcontainer docs: [2f53681](https://github.com/verronpro/office-stamper/commit/2f53681)

---

## Why this stands out

Upgrading the foundation of a project is rarely glamorous, but it's exactly what keeps a small codebase alive and useful to large organizations over time. In February 2023, the project moved to Java 17 (the then‑current LTS), ported its tests to JUnit 5, and documented a ready‑to‑code dev container. The combination matters: a modern runtime unlocks language and performance improvements; a modern test stack streamlines developer experience; and a containerized setup makes sporadic contributors effective within minutes instead of hours.

For a solo‑maintained project used in big‑company projects, those are not "nice to have" tweaks—they are survival tools. Enterprises typically pin on LTS JDKs and require predictable builds; contractors rotate in and out; security scanners and compliance checks look for sane defaults. Shipping these changes in one focused epoch reduces friction for everyone who touches the code or the CLI.

---

## What the commits reveal

### [49b6c09](https://github.com/verronpro/office-stamper/commit/49b6c09) — Java 17 migration

This commit systematically upgraded the entire build toolchain from Java 11 to Java 17:

- **Build files:** Updated all GitHub Actions workflows (`codeql.yml`, `maven.yml`, `pages.yml`, `release.yml`) to use JDK 17 with the Temurin distribution, ensuring consistent behavior across CI environments.
- **Compiler configuration:** Changed Maven compiler plugin `source` and `target` from `1.8` to `17`, enabling modern language features and performance optimizations.
- **Dependency updates:** Upgraded Spring dependencies from 5.x to 6.0.5, aligning with Java 17 requirements and bringing in framework improvements.
- **JVM arguments cleanup:** Removed workaround flags (`--add-opens java.base/java.lang=ALL-UNNAMED`) from Javadoc plugin configuration, indicating that compatibility hacks from earlier JDK transitions were no longer needed.
- **Build tool integration:** Updated `.lift.toml` to reflect JDK 17, ensuring third-party analysis tools run against the correct version.

The changes touch five workflow files and the main `pom.xml`, demonstrating a holistic approach: every build path (local, CI, documentation generation, release) moves forward together. This eliminates version mismatch surprises and ensures the LTS promise holds everywhere.

### [428d5d5](https://github.com/verronpro/office-stamper/commit/428d5d5) — JUnit 5 migration

This large commit completed the transition from JUnit 4 to JUnit 5 across the entire test suite (50 files changed):

- **Dependency swap:** Replaced `junit:junit:4.13.2` with `org.junit.jupiter:junit-jupiter:5.9.2`, the modern JUnit platform.
- **Annotation migration:** Converted `@Test`, `Assert.*` to JUnit 5's `org.junit.jupiter.api.Test` and static assertion imports (`assertEquals`, `assertNotNull`, etc.), making tests more expressive and IDE-friendly.
- **Code cleanup:** Reformatted test classes with consistent indentation (tabs), removed unused proxy builder stub (`ProxyBuilder.java`), and fixed typos in test names (`TabsIndendationTest` → `TabsIndentationTest`).
- **API improvements:** Updated internal utilities like `ExpressionResolver` and `CommentUtil` to return `Optional` instead of null, improving null-safety and aligning with modern Java idioms.
- **Test structure refinement:** Simplified test assertions and removed scattered `Assert.` prefixes in favor of static imports, reducing visual noise and making test intent clearer.

The migration wasn't just find-and-replace; it involved rethinking test patterns (optional returns, cleaner assertions) and establishing a consistent style that future contributors can follow without re-learning old conventions.

### [2f53681](https://github.com/verronpro/office-stamper/commit/2f53681) — Dev container documentation

This commit added a `.devcontainer/devcontainer.json` file specifying:

- **Base image:** Microsoft's Java 17 dev container image (`mcr.microsoft.com/devcontainers/java:0-17-buster`)
- **Build tools:** Maven pre-installed (Gradle explicitly excluded), matching the project's build system
- **Environment guarantee:** A single-command setup that gives any contributor—internal or external—the exact environment the maintainer uses

The 27-line file is executable documentation: it tells VS Code, GitHub Codespaces, or any devcontainer-compatible tool exactly how to provision a working workspace. For sporadic contributors or enterprise teams evaluating the project, this removes hours of "works on my machine" debugging.

---

## Agile/craftsmanship/docs-as-code lens

- **Small, verifiable changes:** Grouping platform upgrades behind green CI reduces the solo maintainer's cognitive load and limits the blast radius of issues.
- **Clarity over cleverness:** JUnit 5's parameterized tests and extensions make intent visible. When tests read like documentation, they *are* documentation-as-code.
- **Eliminate setup tax:** The dev container is executable onboarding. It encodes the environment contract so a contributor—or future you—doesn't rediscover dependencies on every machine.

---

## Solo maintainer + enterprise usage angle

When you're the only maintainer, **the build must be boring**. Java 17 is broadly supported by corporate JDK distributions and build farms; JUnit 5 is the default in most organizations. That means fewer surprises when a large company consumes office-stamper in a sporadic project or invokes the CLI in a constrained runner.

The dev container is a welcome mat: it tells a security or platform team exactly what's needed to reproduce the environment. For teams adopting the tool, this translates into predictable onboarding—a developer can clone, open the container, run tests, and start reading code that uses the same testing style they use elsewhere. That minimizes context switching and builds confidence.

---

## Risks and mitigations

### Risk: Dependency drift or subtle runtime changes with Java 17.  
**Mitigation:** Run the full test suite on multiple JDK vendors; pin versions in Maven; document supported JDKs explicitly.

### Risk: Contributors stuck on older JDKs.  
**Mitigation:** Keep the CLI runnable on standard JREs; document the minimal runtime for end users separately from the development baseline.

### Risk: Devcontainer lock-in.  
**Mitigation:** Treat it as optional scaffolding; keep plain Maven commands the source of truth so local builds remain first-class.

---

## How to apply this

1. **Plan platform upgrades as small steps:** Upgrade the JDK, fix the build, migrate tests, then document the environment. Ship each step with CI green.
2. **Use JUnit 5 features** to make tests intention-revealing (parameterized tests, `@Nested`, temporary directories).
3. **Provide an executable environment spec** (devcontainer, `Dockerfile`, or wrapper) that a newcomer can run untouched. Pair it with a short "Getting started" doc.

---

## References

- **Commits:** [49b6c09](https://github.com/verronpro/office-stamper/commit/49b6c09) (Java 17), [428d5d5](https://github.com/verronpro/office-stamper/commit/428d5d5) (JUnit 5), [2f53681](https://github.com/verronpro/office-stamper/commit/2f53681) (devcontainer)
- **Build:** Maven + Surefire/JUnit 5
- **Docs:** CONTRIBUTING and devcontainer documentation
