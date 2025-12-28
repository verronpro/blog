---
layout: article
title: "From Walkers to Streams"
date: 2024-07-01
categories: [ office-stamper ]
tags: [ office-stamper, java, refactoring, clean-code ]
author: Joseph
description: Deleting legacy traversal boilerplate to reveal the multi-phase nature of the stamping engine.
---

Sustainable maintenance often starts with deleting code that forces you to lie about your intentions. This month, I finally removed the `CoordinatesWalker` — a legacy artifact from the original `docx-stamper` library — and replaced it with functional streams. 

The result is a core that is easier to read, a traversal logic that is more deterministic, and a clear "clearing of the deck" for the final 2.0 release under the unified **OfficeStamper** branding.

## Context
The `CoordinatesWalker` used a Visitor-like pattern that made certain methods mandatory to implement, even when they weren't needed. This created "clunky" code where the actual logic was buried under boilerplate overrides. It distracted from the purpose of the components and made the engine feel heavier than it needed to be.

## Change summary
- **Deleted `CoordinatesWalker` & `BaseCoordinatesWalker`**: Removed the mandatory override requirement in favor of targeted stream processing.
- **Refactored `CommentProcessorRegistry`**: Moved to a three-phase traversal that explicitly separates how different directives are handled.
- **Introduced `ExceptionUtil`**: A new observability bridge that allows users to choose between "fail-fast" (throwing) or "fail-soft" (logging) behaviors for SpEL expressions.
- **Branding Alignment**: Standardized the project name to **OfficeStamper** (removing the hyphen and fixing the casing) across the README and governance files.

## The Three-Phase Advantage
By switching to `DocumentUtil.streamParagraphs()`, I could break down the `CommentProcessorRegistry` into three distinct, readable phases. This reveals the actual hierarchy of how OfficeStamper interprets a document:

1.  **Run Comments**: Directives integrated inside a paragraph (specific text spans).
2.  **Paragraph Comments**: Directives spanning one or several paragraphs.
3.  **Inline Placeholders**: Placeholders acting as comments—crucial for headers and footers where official Word comments are technically prohibited.

```java
// Simplified view of the new registry logic
DocumentUtil.streamParagraphs(document)
    .flatMap(p -> p.getContent().stream())
    .filter(R.class::isInstance)
    .forEach(run -> runProcessorsOnRunComment(...));

DocumentUtil.streamParagraphs(document)
    .forEach(paragraph -> runProcessorsOnParagraphComment(...));

DocumentUtil.streamParagraphs(document)
    .forEach(paragraph -> runProcessorsOnInlineContent(...));
```

## Impact: Observability without the Noise
A recurring friction in template engines is what to do when an expression fails. Does it break the build, or do you just get an empty field? 

The new `ExceptionUtil` gives that choice back to the user via configuration. If you choose to log and continue, I've added `TRACE` level logging to ensure that maintainers still have the diagnostic depth needed to understand *why* an expression failed without cluttering the production logs for everyone else.

## Next steps
- Complete the transition of all internal components to the stream-based traversal.
- Finalize the 2.1.0 release notes with the new OfficeStamper branding.
- Expand characterization tests to cover the new three-phase processing order.

---
*Referenced Commits:* `367b16c` (Walkers to Streams), `5241817` (OfficeStamper branding).
