---
layout: article
title: "Office-stamper 3.1: Stabilizing the Surface"
date: 2026-01-01
categories: [ office-stamper ]
tags: [ office-stamper, java, api, refactoring ]
author: Joseph
description: Consolidating the public API, hiding internal utilities, and refining the post-processing pipeline for the 3.1 release.
---

Following the major overhaul of v3.0, my focus for `office-stamper` has shifted from "making it work" to "making it stable." Last week, I [detailed how I deleted 12,000 lines]({{ site.baseurl }}{% post_url 2025-12-29-monthly-commit-test-suite-modernization %}) of legacy test utilities. Today, I'm bringing that same pruning shears to the production engine with the release of version 3.1.

The goal of this release is simple: reduce the "surface area" of the library to ensure that future improvements don't break user code.

## The Problem: API Leakage

In a project's growth phase, it's tempting to make everything `public`. However, as a solo maintainer, every public class is a promise I have to keep. Version 3.0 exposed several internal utilities—like `SectionUtil` or `ParagraphCollector`—that were never intended for end-users but were necessary for the core engine's transition.

Leaving these exposed creates a "technical debt of expectations." Users might start relying on them, making it impossible for me to refactor the internals without a major version bump.

## The Solution: Consolidation and Encapsulation

Version 3.1 introduces several breaking changes aimed at clarifying what is "API" and what is "Implementation."

### 1. The `.api` Package Consolidation
I've moved core extension interfaces to a central `pro.verron.officestamper.api` package. If you are building custom hooks or removers, you'll need to update your imports for:
*   `PlaceholderHooker`
*   `CommentHooker`
*   `HookRemover`

This move signals that these are the primary touchpoints for extending `office-stamper`. Everything else is now strictly internal.

### 2. Pruning the Public Surface
I've removed or made private over a dozen internal utility classes. These were effectively "implementation leaks." By hiding them, I've reduced the library's public footprint, making it easier for new users to find the classes they *actually* need to interact with.

### 3. Unified Post-processing with `Postprocessors`
The legacy `cleanTags` functionality, which was scattered across different configuration methods, has been replaced by a more flexible and unified pipeline. 

Instead of searching for a `cleanTags` boolean or a specific method, you now use the `Postprocessors` factory:

```java
// Modern v3.1 configuration
var configuration = OfficeStamperConfigurations.standard()
    .addPostprocessor(Postprocessors.removeTags("placeholder"));

var stamper = OfficeStampers.docxStamper(configuration);
```

This change aligns with the "Pipeline" philosophy I've been advocating for: stamping is just one step in a document's lifecycle. Post-processors allow you to stack operations (cleaning tags, removing orphaned notes, etc.) in a clear, declarative way.

## Sharp Edges: Logging Verbosity

One subtle change in 3.1 is the refinement of logging levels. I've moved many verbose messages—especially those related to the internal hook traversal—from `DEBUG` to `TRACE`. 

If you are troubleshooting a complex template and feel like the engine has gone "silent," you may need to adjust your logger configuration to see the full execution flow. This was a necessary trade-off to keep `DEBUG` output readable for the majority of users.

## Impact: Maintenance at Scale

By consolidating the API and hiding the "dirty" internals, I've made `office-stamper` 3.1 more resilient. 
*   **For Users**: A cleaner, more discoverable API with fewer "dead ends."
*   **For Me**: The freedom to refactor the core engine without fearing a cascade of breaking changes across the ecosystem.

## Next Steps

With the surface now stabilized, I'm turning my attention toward making the engine more accessible. The next few monthly commits will focus on providing a service API based on the library. This will allow `office-stamper` to be deployed as a standalone service, lowering the barrier for non-Java environments to generate high-fidelity documents.

---

### January 1st 2026 — Commit Summary:
- **Breaking**: Reorganized core interfaces into `pro.verron.officestamper.api`.
- **Breaking**: Replaced legacy `cleanTags` methods with `Postprocessors.removeTags(String)`.
- **Refactor**: Hidden or removed internal utility classes (`SectionUtil`, `TagsVisitor`, etc.).
- **Refactor**: Refined logging levels, shifting verbose engine traces to `TRACE`.
- **Feat**: Moved experimental features to a dedicated `experimental` package.
