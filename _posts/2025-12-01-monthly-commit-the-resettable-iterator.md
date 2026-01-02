---
layout: article
title: "The Resetable Iterator: Engineering Predictable Order"
date: 2025-12-01
categories: [ office-stamper ]
tags: [ office-stamper, java, refactoring, v3 ]
author: Joseph
description: Moving from Java Streams to a resetable iterator to handle document mutations and ensure strictly predictable processing order in v3.
---

As we approach the final release of `office-stamper` v3, the focus has shifted from "adding features" to "ensuring predictability." One of the most significant architectural shifts in this version is how we traverse the document. 

This month, I replaced our paragraph streaming logic with a custom, resetable iterator. It might sound like a step back from modern functional Java, but in the world of document stamping, it is a massive leap forward for reliability.

## The Problem: Mutation during Iteration

In previous versions, `office-stamper` worked in a "scan then execute" mode. It would scan the document, find all the places that needed changing (comments, placeholders), and then run the processors on those spots.

The issue? A `CommentProcessor` can technically change *anything* in the document at *any* point. If a processor repeats a section or deletes a paragraph, the list of "change points" we just scanned might now be invalid. Elements move, indices shift, and "zombie" elements that no longer exist might still be in our queue.

## The Solution: `DocxIterator` & `ResetableIterator`

For v3, we need to run every processor every time we encounter a new element to process. If that processor modifies the document, the engine needs to be aware of it immediately.

The solution is the `DocxIterator`. Instead of a one-way stream, we now use an iterator that can be **reset**.

```java
var iterator = DocxIterator.ofParagraphs(source);
while (iterator.hasNext()) {
    var paragraph = iterator.next();
    // If a processor changes the document structure...
    if (runProcessorsOnInlineContent(expressionContext, paragraph) > 0) {
        iterator.reset(); // We literally "go back in time" to the start
    }
}
```

## Why it matters for v3

1.  **Strict Document Order**: We now process elements exactly as they appear. If a comment at the top of the page changes something at the bottom, the bottom element will be processed in its *new* state.
2.  **Handling Insertions**: If a `RepeatProcessor` inserts 10 new rows, the `reset()` ensures the engine sees those new rows and processes any placeholders inside them correctly.
3.  **Predictability**: By "going back at the start" after a mutation, we eliminate a whole class of bugs where the engine and the actual XML state get out of sync.

## The Trade-off: Control over Elegance

Yes, we stopped using the more straightforward Java Stream approach. Streams are elegant for data processing, but they are notoriously difficult to use when the underlying source is being mutated by the consumer. 

For a library like `office-stamper`, I've decided that **control and predictability** are worth more than "modern" syntax. As a solo maintainer, I want to sleep soundly knowing that a user's complex nested loop won't cause an `IndexOutOfBoundsException` because of an edge-case mutation.

## Impact

*   **For Users**: More complex templates with nested logic will "just work" without weird ordering artifacts.
*   **For Contributors**: The core loop in `CommentProcessorRegistry` is now significantly easier to reason about.

---

### December 1st 2025 — Commit Summary:
- **Feat**: Introduced `DocxIterator` and `ResetableIterator`.
- **Refactor**: Replaced paragraph streaming in `CommentProcessorRegistry` with the new iterator.
- **Logic**: Implemented `reset()` logic to handle document mutations safely.
- **Chore**: Updated `docx4j` to `11.5.2`.
