---
layout: article
title: "Refactoring for Contextual Clarity"
date: 2024-11-01
categories: [ office-stamper ]
tags: [ office-stamper, java, refactoring, craftsmanship ]
author: Joseph
description: Introducing ProcessorContext and domain-adjacent logic to remove primitive obsession and clarify engine contracts.
---

## Context — The "Boy Scout" of Document Processing

In October 2024, I found myself squinting at the `CommentProcessorRegistry`. The engine had developed three distinct (and slightly inconsistent) ways of handling logic: inline placeholders acting as processors, processors restricted to a single paragraph, and those spanning multiple paragraphs. 

The code was suffering from **primitive obsession**, passing around raw maps and loosely typed parameters that made the "state of the world" during stamping hard to track. Following the **Boy Scout Rule**—leaving the campground cleaner than I found it—I decided to introduce a formal `ProcessorContext` and refine our domain boundaries to make these behaviors explicit and, eventually, identical.

## Change Summary

- **Introduced `ProcessorContext`**: A Java `record` to encapsulate the stamping state (DocxPart, resolvers, replacers) in an immutable, brief container.
- **Replaced Cryptic Maps**: The `Map<Class<?>, ?>` used for processor lookups was replaced by a dedicated `CommentProcessors` class, improving type safety and discoverability.
- **Hardened Domain Boundaries**: Logic moved from static utilities (`CommentUtil`) into the `Paragraph` and `Comment` interfaces.
- **Removed Invalid States**: By encapsulating complex utility methods, I've made certain illegal engine states impossible to represent.

## Code — From Cryptic Maps to Structured Context

The centerpiece of this refactoring is the `ProcessorContext`. By using a Java `record`, I communicate immutability and intent without the boilerplate of a traditional class.

```java
/**
 * Encapsulates the context required by processors during the stamping process.
 */
public record ProcessorContext(
        DocxPart docxPart,
        CommentProcessors processors,
        ExpressionResolver resolver,
        PlaceholderReplacer replacer
) {
}
```

Similarly, the transition from a "magic map" to a first-class `CommentProcessors` object removes the cognitive load of wondering what keys and values are expected:

```java
// Before: Cryptic and dangerous
Map<Class<?>, Object> processorMap = ...;
Object p = processorMap.get(CustomProcessor.class);

// After: Explicit and discoverable
public class CommentProcessors {
    private final Map<Class<?>, Object> processorMap;
    
    public <T> T getProcessor(Class<T> processorClass) {
        return processorClass.cast(processorMap.get(processorClass));
    }
}
```

## Impact

1. **Reduced Cognitive Load**: Developers no longer need to keep the "map of processors" in their head; the IDE provides better autocompletion and type checking.
2. **Predictable Behavior**: Differentiating between inline, single-paragraph, and multi-paragraph processing is now an explicit part of the engine's flow.
3. **Easier Testing**: With `ProcessorContext`, we can mock or stub the entire "world" of a processor in a single, self-contained object.
4. **Natural Manipulation**: The new `Paragraph` interface makes Word XML manipulation feel like a native domain operation rather than a low-level DOM surgery.

## Next Steps

This refactoring is part of a larger, ongoing effort to remove primitive obsession across the library. While the `ProcessorContext` is stable, I expect to tweak its contents as I experiment with more advanced "docs-as-code" features. The ultimate goal remains: making the engine’s internal mechanics as readable as the templates it processes.

---

### Checklist — Refactoring Context
1. **Identify Multi-parameter Drifts**: Look for methods taking 3+ related engine components.
2. **Wrap in Records**: Use Java `record` for immutable state passing.
3. **Kill the Maps**: Replace generic maps with domain-specific collection wrappers.
4. **Move Logic Inward**: If a utility only touches one domain object, move it into that object's interface.
