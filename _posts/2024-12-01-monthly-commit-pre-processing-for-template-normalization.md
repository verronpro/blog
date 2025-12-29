---
layout: article
title: "Normalizing Template Chaos with Pre-Processing"
date: 2024-12-01
categories: [ office-stamper ]
tags: [ office-stamper, java, preprocessing, refactoring, craftsmanship ]
author: Joseph
description: Moving template normalization into a dedicated PreProcessor stage to simplify the core engine and handle real-world document quirks.
---

## Context — The "Real World" Word Document

In November 2024, I spent a significant amount of time wrestling with "imbricated" comments—nested or overlapping comment ranges in Word documents that were causing the core stamping engine to trip over itself. Word also loves to embed `proofError` tags and other irrelevant XML noise that, while harmless to Word, mess up our indexing, test characterizations, and general reasoning about the document structure.

The core engine was becoming cluttered with edge-case handling for these "messy" inputs. I realized that instead of teaching the engine how to survive in a chaotic environment, I should simply clean the environment before the engine starts its work. This led to the introduction of first-class **Pre-Processing** as a dedicated stage in the `DocxStamper` pipeline.

## Change Summary

- **Introduced `PreProcessor` API**: A formal interface to allow document manipulation before any comment processors are triggered.
- **Moved Normalization Upstream**: Logic for removing malformed or incomplete comments (those that open but never close) was moved into the `RemoveMalformedComments` pre-processor.
- **Killed the Legacy `DocumentWalker`**: By guaranteeing cleaner inputs, I was able to retire the complex `DocumentWalker` class, replacing its heavy lifting with simpler, focused methods.
- **Enhanced `Stringifier` for Diagnostics**: To discover these inconsistencies, I expanded our test utilities to provide more useful string representations of the Word XML, making failures obvious and early.

## Code — Heroes of the Refactor

The "hero" of this month's work is the explicit configuration of the pipeline. Instead of a monolithic engine, we now compose it:

```java
public static OfficeStamperConfiguration standardWithPreprocessing() {
    return new OfficeStamperConfiguration()
            .addPreProcessor(new RemoveMalformedComments())
            .addPreProcessor(new RemoveProofErrors())
            .addPreProcessor(new ConsolidateSplitRuns());
}
```

The `RemoveMalformedComments` implementation is now a self-contained unit that ensures the core engine never sees a comment range it can't handle:

```java
public class RemoveMalformedComments implements PreProcessor {
    @Override 
    public void process(WordprocessingMLPackage document) {
        var commentElements = WmlUtils.extractCommentElements(document);
        // ... identify opened comments that are never closed ...
        // ... prune them from the document ...
        log.debug("Malformed comments pruned before core processing.");
    }
}
```

## Impact

1.  **Engine Simplicity**: The core stamping loop is now significantly smaller. It no longer needs "defensive" logic against nested comments or split runs; it simply assumes the input is valid.
2.  **"Magic" Fixes for Users**: Templates that previously failed with cryptic XML errors or incorrect indexing now "just work." If they do fail, they fail early during the pre-processing stage with clear diagnostic logs.
3.  **Better Testability**: The enhanced `Stringifier` utility has turned our characterization tests into a superpower. We can now see exactly how the Document evolves stage-by-stage, from raw input to normalized template to stamped output.

## Next Steps

While the `PreProcessor` API is stable, I'm already looking at **Post-Processing** to tidy up the *outputs*—collapsing empty paragraphs or removing scaffolding that shouldn't be visible to the end-user. The ultimate goal is a perfectly clean pipeline where the business logic of stamping is completely isolated from the technical quirks of the WordProcessingML format.

---

### Checklist — Template Normalization
1.  **Prune the Noise**: Remove `proofError` and `noProof` tags before processing.
2.  **Validate Ranges**: Ensure all comment ranges have matching start and end points.
3.  **Consolidate Runs**: Merge adjacent runs with identical formatting to simplify placeholder detection.
4.  **Fail Early**: Use pre-processors to validate template invariants before committing to a heavy stamping run.
