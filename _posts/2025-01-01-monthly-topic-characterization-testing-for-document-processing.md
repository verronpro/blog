---
layout: article
title: "Readable Document Testing: The Power of Characterization"
date: 2025-01-01
categories: [ office-stamper ]
tags: [ craftsmanship, testing, office-stamper, java, docs-as-code ]
author: Joseph
description: "Why moving from brittle XML-based unit tests to holistic characterization tests is a game-changer for complex document processing engines."
---

In the world of document automation, we often fall into the trap of testing the *how* instead of the *what*. For years, `office-stamper` tests were heavily focused on the underlying XML structure of WordProcessingML. We would assert that a specific `w:p` (paragraph) contained a specific `w:r` (run), and that the run had the correct `w:t` (text).

Here's the stark reality: Users aren't concerned with XML operations. Their focus is on whether the intended duplication of a line occurred, and if the table row designated for removal has indeed been removed.

As part of the ongoing evolution of `office-stamper`, I’ve been shifting our testing strategy toward **Characterization Testing** (also known as Golden Master testing). The goal is to test high-level features holistically while making the tests themselves readable enough to serve as documentation.

## The Technique Taxonomy: XML vs. Stringification

### 1. Low-Level XML Assertions (The Legacy)
Initially, tests used XPath or deep object traversal to verify output. 
- **Pros**: Precise.
- **Cons**: Brittle. A minor refactor in how runs are split (common in Word) would break dozens of tests even if the visual output was identical.

### 2. Characterization via Stringification (The Future)
Instead of looking at XML, we turn the document into a simplified textual representation. We then compare this "stringified" document against a baseline.
- **Pros**: Holistic, readable, and captures the user's perspective.
- **Cons**: Requires a carefully tuned "Stringifier" to avoid noise.

## The `Stringifier` Utility: Docs-as-Code for Tests

The heart of this new approach is a `Stringifier` utility. It traverses the `WordprocessingMLPackage` and converts complex elements into a human-readable format. 

What makes it powerful is the **Visibility Heuristic**: *If a normal user cannot see the difference when opening the document in Word, the difference shouldn't exist in our test representation.*

For example, we map Word styles to simple text markers:

```java
private Function<? super String, String> decorateWithStyle(String value) {
    return switch (value) {
        case "heading 1" -> "== %s\n"::formatted;
        case "heading 2" -> "=== %s\n"::formatted;
        case "caption"   -> ".%s"::formatted;
        default          -> "[%s] %%s".formatted(value)::formatted;
    };
}
```

This turns a multi-megabyte XML structure into something like this in our test assertions:

```text
== Simpson Family
[Normal] Homer Simpson
[Normal] Patriarch
[Normal] "D'oh!" is Homer's trademark exclamation.
```

## Impact: Sped-up Development and Hidden Bug Discovery

Moving to large textual assertions in tests like `ConditionalDisplayTest` has provided two unexpected benefits:

1.  **Increased Velocity**: When I refactored the internal configuration and registry system, I didn't have to fix dozens of broken XML paths. As long as the stringified output matched, I knew the feature was intact.
2.  **Holistic Awareness**: Because these tests watch the *entire* document, they catch regressions that targeted unit tests miss—like a paragraph accidentally losing its spacing or a footer being unintentionally stripped.

## The Solo Maintainer's Perspective: Managing Risk

Does this replace API testing? **Absolutely not.** Characterization tests are great for verifying behavior, but they won't stop you from breaking your public API. 

As a solo maintainer, I use a layered defense:
- **`core` package isolation**: The messy internals stay in `core`, which clients aren't supposed to touch.
- **`api` and `preset` packages**: These are the stable extension points.
- **CLI Client**: I maintain a "vanilla" client in the `cli` module. If the CLI still works, the primary user path is safe.

Characterization tests sit alongside these, providing the confidence to refactor the "scary" parts of the engine without fear.

## Pitfalls to Avoid

- **Noise Pollution**: If you include XML IDs or timestamps in your string representation, your tests will fail on every run. Apply the visibility heuristic ruthlessly.
- **The "Over-Green" Trap**: It is easy to just update the baseline when a test fails. Always review the `diff` to ensure the change in output is actually what you intended.

## Checklist — Implementing Characterization Tests

1.  **Define Visibility**: Decide which formatting elements (bold, headers, lists) actually matter to your users.
2.  **Automate Stringification**: Build a utility that converts your output format to a stable, text-based representation.
3.  **Establish Baselines**: Run your existing, trusted code to generate "Golden Masters."
4.  **Review Diffs**: Treat test failures as a conversation. "Is this change in the document intentional?"
5.  **Isolate the Core**: Ensure your high-level tests aren't coupled to the same interfaces you're trying to refactor.

By focusing on what the user sees, we make our tests more resilient, our documentation more accurate, and our maintenance more sustainable.
