---
layout: article
title: "Engine Refactoring: The Road to v3"
date: 2025-11-01
categories: [ office-stamper ]
tags: [ office-stamper, java, refactoring, craftsmanship ]
author: Joseph
description: Simplification of the core engine to remove unpredictable behavior and pave the way for non-greedy placeholder resolution in v3.
---

October has been a month of deep structural cleaning for `office-stamper`. As we approach the next major version, I’ve taken a hard look at the core engine’s abstractions. The goal is simple: make the library’s behavior more predictable for users and more maintainable for "future me."

The most significant changes involve how we handle comments and runs, effectively simplifying the core loop to remove "quirky" edge cases that have haunted complex templates for a while.

## Context: The "Run-Only" Fragility

Historically, `office-stamper` had a specialized logic for "run-only" processors. These worked if and only if a comment selected a single run in the Word XML. 

While this seemed like a logical optimization, it created a major UX problem for business users. In Microsoft Word, it is trivial to accidentally create empty runs or have a single visual selection span multiple underlying XML runs. A user would highlight a word, add a comment, and sometimes it would work, and sometimes it wouldn't—with no visual feedback as to why.

As a maintainer, I want to eliminate these "it depends on the XML" moments. If you can see it in Word, `office-stamper` should be able to stamp it.

## Change Summary

- **Engine Simplification**: Removed `runProcessorsOnRunComment` from the core registry. All comments are now handled at the paragraph level, ensuring a consistent resolution strategy regardless of how Word decided to fragment the runs.
- **API Deprecation**: Marked `setCurrentRun`, `getCurrentRun`, and related methods in `AbstractCommentProcessor` and `ProcessorContext` as deprecated. 
- **Processor Streamlining**: Updated `ReplaceWithProcessor` to eliminate redundant single-run handling logic, favoring a unified `commentRangeStart`/`commentRangeEnd` approach.
- **Dependency Refresh**: Continued our "rolling baseline" strategy by keeping dependencies like Jackson and JUnit at their absolute latest versions.

## Towards v3: Non-Greedy Resolution

The most exciting outcome of this refactoring is that it clears the path for **Non-Greedy Resolution**, a game-changer for v3.

Until now, when a `CommentProcessor` (like a `RepeatProcessor`) contained placeholders or inner comments, it had to launch the replacement process **eagerly**. It had to resolve everything inside its scope immediately to provide the correct context to the inner elements.

In v3, we are moving toward a "context stack" approach. Instead of eager replacement, processors will be able to mark inner elements with a hint or a scope. This allows:
1.  **Context Preservation**: Inner placeholders can refer to the root context even when nested deep inside multiple loops.
2.  **Cleaner SpEL**: A more natural way to resolve values using SpEL without the engine having to "guess" the intended scope.
3.  **Better Imbricated Comments**: Removing single-run tracking makes the resolution order much simpler to reason about.

## Impact

1.  **Predictability**: Template designers no longer need to worry about the internal XML run structure of their documents.
2.  **Maintainability**: The core loop is significantly smaller and easier to debug. We’ve removed a whole category of "run-level" edge cases.
3.  **Consistency**: Custom processor authors now have a unified, paragraph-oriented context to work with.

## Next Steps

With the engine's core loop streamlined, the next phase is implementing the context stack for SpEL. This will be the cornerstone of `office-stamper` v3.0.0. We are also finalizing the migration of our automated governance from Renovate to Mend to ensure our security posture remains top-notch as the library grows.

---

### November 1st 2025 — Commit Summary:
- **Refactor**: Removed redundant `runProcessorsOnRunComment` logic.
- **API**: Deprecated single-run tracking in `CommentProcessor` and `ProcessorContext`.
- **Logic**: Streamlined `ReplaceWithProcessor` to use paragraph-range replacement.
- **Chore**: Updated `jackson` to `2.20.1` and `junit-jupiter` to `6.0.1`.
- **Docs**: Updated README to reflect version `2.9.0` status.
