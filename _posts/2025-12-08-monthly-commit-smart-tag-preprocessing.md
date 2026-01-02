---
layout: article
title: "Smart Tag Preprocessing: Taming Word’s Run Fragmentation"
date: 2025-12-08
categories: [ office-stamper ]
tags: [ office-stamper, java, robustness, templates ]
author: Joseph
description: Using Word Smart Tags as a robust container for placeholders to prevent fragmentation issues and improve engine extensibility.
---

If you’ve ever used a template engine for Microsoft Word, you’ve likely encountered the "split run" nightmare. You type `${name}` in Word, but internally, Word saves it as three separate XML runs: `<w:r>${</w:r>`, `<w:r>na</w:r>`, `<w:r>me}</w:r>`. 

When this happens, a simple search-and-replace engine fails because it never "sees" the full expression. This month, I’ve introduced a structural solution to this problem in `office-stamper`: **Smart Tag Preprocessing**.

## Why Smart Tags?

When looking for a container to "shield" our placeholders from Word's fragmentation, I evaluated several options:
*   **Bookmarks**: Too restrictive; they can't easily hold complex XML structures.
*   **CustomXML**: Deprecated in newer Word versions and complex to manage.
*   **Run Merging**: Hard to implement reliably without breaking formatting.

I chose **Smart Tags** (`<w:smartTag>`). They are flexible, can hold any property, and most importantly, they act as a stable wrapper. By wrapping `${expression}` in a Smart Tag *before* the main processing pass, we ensure that no matter how Word decides to split the underlying runs, the expression remains logically grouped.

## The `PreparePlaceholders` Pre-processor

The implementation uses a new pre-processing phase. It scans the document for `${...}` patterns and wraps them:

```java
public class PreparePlaceholders implements PreProcessor {
    @Override
    public void process(WordprocessingMLPackage document) {
        var pattern = Pattern.compile("(\\$\\{([^{]+?)})", Pattern.DOTALL);
        // ... scans paragraphs and wraps matches in Smart Tags ...
    }
}
```

## Extensibility for the Community

This isn't just a hardcoded fix. I've refined the `PreProcessor` and `PostProcessor` interfaces that were introduced earlier this year. 

I envision a future where users can write their own processors to:
*   **Translate** templates on the fly before stamping.
*   **Obfuscate** sensitive placeholder names.
*   **Apply custom formatting** logic that the standard engine doesn't cover.

## Impact: Predictability for Everyone

*   **For Template Designers**: You don't have to change anything! Keep writing your expressions as usual. The engine now handles the "XML magic" to make them robust.
*   **For Processor Authors**: This is a shift in mindset. Instead of looking for raw text runs, you should now leverage the structure provided by these tags.

By moving this logic to a dedicated `PreProcessor`, we’ve kept the core engine clean while significantly increasing its "it just works" factor.

---

### December 8th 2025 — Commit Summary:
- **Feat**: Added `PreparePlaceholders` pre-processor using Smart Tags.
- **Refactor**: Improved `PreProcessor` and `PostProcessor` interface definitions.
- **Logic**: Implemented robust regex-based placeholder identification and wrapping.
- **Test**: Added comprehensive tests for fragmented run scenarios.
