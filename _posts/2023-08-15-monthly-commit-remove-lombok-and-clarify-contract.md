---
layout: article
title: "Fewer Magic Annotations, Clearer Contracts: Removing Lombok and Aligning Javadoc"
date: 2023-08-15 09:00:00
categories: [ maintainability ]
author: joseph-verron
tags: [ office-stamper, java, api, docs-as-code, maintainability ]
description: Removing Lombok and making defaults explicit in code and Javadoc to improve clarity, tooling, and
  onboarding.
---

TL;DR: Removed Lombok and made contracts explicit in code and Javadoc. Outcome: clearer stack traces, predictable
reflection, and easier onboarding for sporadic enterprise contributors.

Commit:
[0419cc4c](https://github.com/verronpro/office-stamper/commit/0419cc4c)

## Why this is July's standout

Solo‑maintained projects live or die by clarity. I removed Lombok and aligned Javadoc with explicit defaults across the
codebase. This is mundane work that pays compound interest: fewer magic annotations, clearer constructor and getter
semantics, and documentation that matches what the compiler sees. For enterprise teams who will adopt Office‑stamper for
a short project and then disappear for months, explicit code is a feature—onboarding is faster, IDE navigation is
predictable, and debugging doesn't require a Lombok mental model.

Lombok can be a pragmatic accelerator, but it introduces invisible structure: generated methods, implicit nullability
assumptions, and annotations that sometimes surprise build tools or static analysis. By removing it, we trade terse
class bodies for explicitness and toolchain portability. Javadoc then becomes a reliable companion: it documents what
the code really does, and readers don't have to cross‑reference Lombok's rules to understand whether a field is mutable
or which constructor exists.

## What actually changed

- **Removed Lombok dependencies** from `pom.xml` and all source files, eliminating compile-time code generation.

- **Reintroduced explicit constructors and getters** where needed using idiomatic Java. For example,
  `TypeResolverRegistry` now has an explicit `@Getter` for its `defaultResolver` field with clear Javadoc, and
  `IndexedRun` was converted to a Java record for immutability and clarity.

- **Comprehensive Javadoc updates** across 66 files, adding `@param`, `@return`, `@throws`, and class-level
  documentation. Every public API method now documents its contract, defaults, and invariants—no more inferring behavior
  from Lombok annotations.

- **Made utility classes uninstantiable** by adding private constructors that throw `DocxStamperException`, following
  best practices for classes like `CommentUtil`, `DocumentUtil`, `ParagraphUtil`, and `SectionUtil`.

- **Clarified method visibility** by marking constructors and utility methods as `protected` or `private` where
  appropriate, making the API surface more intentional.

- **Improved code consistency** by replacing `text.equals("")` with `text.isEmpty()`, converting utility classes to
  proper patterns, and removing redundant imports.

- **Fixed Javadoc build configuration** by removing `<failOnError>false</failOnError>` from the Maven Javadoc
  plugin—documentation now must be complete and correct.

While these bullets sound like housekeeping, the effect is broader: stack traces are clearer, reflective code behaves
more predictably, and grep‑based discovery works again. It also simplifies contribution: a developer scanning the repo
doesn't need Lombok knowledge to modify a type safely. The codebase went from 651 deletions to 1,732 additions—net
positive for clarity, even if it means more lines.

## Agile and craftsmanship lens

- **Make implicit explicit.** Externalizing hidden machinery (like Lombok's generated getters) reduces
  surprises and accelerates reviews. The `@Getter` annotations are gone; now you see exactly what's public.

- **Optimize for maintainability over brevity.** Fewer layers (annotation
  processors, configuration) means fewer failure modes. Records like `IndexedRun` give you immutability and clarity
  without Lombok's magic.

- **Keep docs and code aligned.** When defaults are documented in Javadoc
  and implemented in code without indirection, the cognitive cost of
  context‑switching shrinks. Every `@param` and `@return` tag now matches what the method actually does.

- **Enforce quality gates.** Re-enabling Javadoc errors in Maven means incomplete documentation breaks the
  build—documentation debt can't accumulate silently.

## Solo‑maintainer + enterprise usage angle

As a solo maintainer, my time is best spent on core features and sharp edges users actually hit. Fighting CI quirks or
IDE plugins because of an annotation processor is not a good trade. For big‑company adopters, audit and security
processes are simpler when the code is plain Java without compile‑time code generation. Explicit constructors and
accessors make it obvious how to instantiate types in restricted environments (locked‑down CI agents, vendor‑approved
IDEs), and they reduce "works on my machine" discrepancies.

The Javadoc improvements also matter for enterprise adoption: security teams can review documentation without running
annotation processors, and generated Javadoc sites work reliably with standard tooling. No special plugins required.

## Risks and mitigations

- **Risk:** Boilerplate growth from explicit getters and constructors.  
  **Mitigation:** Used Java records where appropriate (like `IndexedRun`) and centralized patterns in small helpers. The
  1,732 additions are offset by clarity gains.

- **Risk:** Behavior changes when replacing generated equals/hashCode.  
  **Mitigation:** Converted classes to records where value semantics mattered, and wrote targeted tests for value
  objects. Records give you correct implementations by default.

- **Risk:** Inconsistent Javadoc quality across 66 files.  
  **Mitigation:** Enforced Javadoc completeness by removing `<failOnError>false</failOnError>` from the
  build—documentation must be correct or the build fails.

- **Risk:** Utility class instantiation (now prevented by private constructors).  
  **Mitigation:** Clear error messages via `DocxStamperException` guide developers toward correct usage.

## How to apply this in your project

1. **Audit your code for compile‑time code generation** (annotation processors, bytecode weaving). Remove them where the
   value is marginal compared to the complexity they add.

2. **Make defaults and invariants explicit** in both code and Javadoc. If a field is non‑null, say it and enforce it.
   Use records for immutable value objects.

3. **Prevent utility class instantiation** with private constructors that throw descriptive exceptions.

4. **Enforce Javadoc quality** in your build. Don't let documentation drift from implementation.

5. **Rebuild your contribution docs:** document how to navigate the code without special plugins; keep the on‑ramp
   smooth for sporadic contributors.

## References

- Commit:
  [0419cc4c](https://github.com/verronpro/office-stamper/commit/0419cc4c)

- Comprehensive Javadoc updates across 66 files including all processors, utilities, and core API classes.

- Related commits around July 2023: updates to test setup and CI to
  align with explicit APIs.
