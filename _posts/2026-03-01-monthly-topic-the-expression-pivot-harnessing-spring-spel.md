---
layout: article
title: "The Expression Pivot: Harnessing Spring SpEL for Dynamic Contexts"
date: 2026-03-01
categories: [ office-stamper ]
tags: [ office-stamper, java, api, architecture ]
author: Joseph
description: Retiring the custom ExpressionParser in favor of Spring SpEL to support nested contexts and complex data structures.
---

When I released `office-stamper` v3.0 back in January, I introduced the possibility of using a fully custom `ExpressionParser`. The goal was to give users complete control over how placeholders were evaluated. However, less than three months later, I am retiring that feature.

Sometimes, the best way to move forward is to admit that a custom-built solution, while elegant, can't compete with a well-established standard. For v3.2, we have pivoted entirely to **Spring SpEL (Spring Expression Language)** as our core expression engine.

## The Problem: The Nested Context Trap

The primary driver for this change was the challenge of managing nested contexts within templates. In version 3.0, we introduced hierarchical context resolution, which allowed the engine to keep track of parent scopes. While this worked for simple POJOs and Map with String keys, our custom v3.0 implementation struggled to provide a consistent way to handle more complex structures, such as:
*   **`List`-based contexts**
*   **Deeply nested structures**

## The Solution: A Comeback to SpEL

Technically, the `ExpressionParser` interface we provided in v3.0 was already part of the SpEL project, designed to allow alternative parser implementations. And before v3.0, we were letting users provide a `ParserConfiguration` to control the behavior of the inner `SpelExpressionParser`. So we decided to comeback to the previous status quo and standardizing strictly on Spring's `SpelExpressionParser`. It provides a massive advantage: we can now fully leverage its native ability to manage nested contexts using `#this` and `#root`.

### Why this matters: Solving Issue #644

By re-standardizing on SpEL, we've been able to resolve a regression introduced in version 3.1 (Issue #644). In that release, the `#this` variable—in this case used within `repeatParagraph` blocks—broke when used in conjunction with custom resolvers.

Now, the engine understands `#this` as the leaf of the current context branch (the most nested element being resolved), while `#root` always points back to the original context object. This is now fully functional even when your context is a `List` or a `List<List<Object>>`.

## List of Lists: Finally Supported

This change also allows us to officially support Issue #623 (originally reported by **robfero** in [Discussion #557](https://github.com/verronpro/office-stamper/discussions/557)). Rob was looking for a way to render a list of lists within a document—a task that previously required custom comment resolvers.

With the new engine, you can reliably iterate over deeply nested collections:

```java
// Example of a nested context: List of Lists
List<List<String>> data = List.of(
    List.of("A1", "A2"),
    List.of("B1", "B2")
);

// In the template, you can now use SpEL to 
// navigate these structures with ease.
```

## Impact: Power and Predictability

Removing the custom parser "pluggability" introduced only three months ago should be the right decision. By focusing on the standard, we have:
1.  **Simplified the Engine**: We removed a potential source of bugs and complexity.
2.  **Corrected Nesting**: Variables are now resolved in the right scope every time.

For users, this means your templates can now be far more dynamic. You can use SpEL's full range of operators, method calls, and projection features. For me, as a maintainer, it means I can stop worrying about expression parsing and focus on the document manipulation logic that makes `office-stamper` unique.

---

### March 1st 2026 — Topic Summary:
- **Major**: Replaced custom `ExpressionParser` with **Spring SpEL**.
- **Feat**: Full support for `#this` and `#root` in all template contexts (Fix #644).
- **Feat**: Support for List-based data structures like `List<List<Object>>` (Fix #623).
- **API**: Restandardized on `SpelExpressionParser` behavior.
