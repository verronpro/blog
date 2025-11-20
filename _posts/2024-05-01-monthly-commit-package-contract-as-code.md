---
layout: article
title: "Package contracts as code"
date: 2024-05-01
categories: [ office-stamper ]
tags: [ office-stamper, java, api ]
author: Joseph
description: Codifying package intent and non‑null defaults so tools can enforce the rules we live by.
---

Package‑level contracts, visible to humans and tools. I added `package-info.java` to state intent and enforce non‑null by default. Clear boundaries: `core` is internal and volatile, `api` is the extension surface, `preset` is a safe bootstrap and living examples. 

Commit: [`6d00de1`](https://github.com/verronpro/office-stamper/commit/6d00de1)

## Context
Tiny diff, big leverage. I introduced `package-info.java` files to turn unwritten rules into code the JVM ecosystem understands. The immediate target is `pro.verron.docxstamper.preset`, but the intent is broader: standardize package‑level Javadoc to explain boundaries and apply annotations that help tools enforce invariants.

- `pro.verron.docxstamper.core`: internal. Expect frequent refactors. Don’t extend here.
- `pro.verron.docxstamper.api`: the official extension surface.
- `pro.verron.docxstamper.preset`: opinionated bootstrap (“wheels attached”), and an example catalog for customizers; compose via factory methods.

I prefer null‑free code: `Optional`, Null Object, and simpler cyclomatic paths. `@NonNullApi` helps surface legacy nullability before it leaks into contracts.

## Change summary
- Added package‑level Javadoc to state purpose and boundaries.
- Applied Spring's `@NonNullApi` (Office‑stamper already depends on Spring for SpEL) so non‑null is the default; opt‑outs are explicit via `@Nullable`.
- Centralized expectations where IDEs/Javadoc surface them and reviewers can diff them.

## Core snippet
```java
/**
 * This package provides presets and recommended configurations for docxstamper library users;
 * <p>
 * It should not be extended upon, but it can be used as a bootstrap for projects.
 */
@NonNullApi
package pro.verron.docxstamper.preset;

import org.springframework.lang.NonNullApi;
```

## Impact

- Better consumer ergonomics: clear where to start, clear where not to extend.
- Safer defaults: fewer ambiguous contracts; IDEs flag nullability issues.
- Maintainability: boundaries you can review and diff reduce accidental scope creep.

## What’s next
- Generalize package‑level Javadoc across modules (apply the “contracts as code” pattern to other boundaries).
- Keep relying on IDE signals short‑term; add ArchUnit tests to automate architectural rules later.

References: commit [`6d00de1`](https://github.com/jverron/office-stamper/commit/6d00de1); file path `src/main/java/pro/verron/docxstamper/preset/package-info.java`.
