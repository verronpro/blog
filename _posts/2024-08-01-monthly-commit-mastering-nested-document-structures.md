---
layout: article
title: "Mastering Nested Document Structures"
date: 2024-08-01
categories: [ office-stamper ]
tags: [ office-stamper, java, refactoring, wordprocessingml, sdt ]
author: Joseph
description: Refactoring StandardParagraph to handle nested SDTs and JAXB elements, enabling support for Word Form Controls and office.com templates.
---

In document automation, the "what you see is what you get" promise of Word often hides a complex, nested reality. This month, I addressed a long-standing limitation in how **OfficeStamper** perceives a paragraph. By moving from a flat traversal to a recursive approach, we've unlocked support for Word Form Controls and opened the door to a wider ecosystem of templates.

## Context
The catalyst for this change was [Issue #286](https://github.com/verronpro/office-stamper/issues/286), reported in April. Users found that expressions placed inside "Plain Text Form Controls" (Structured Document Tags, or SDTs) were simply being ignored by the engine. 

The reason was simple: our `StandardParagraph` implementation was looking at the paragraph's content as a flat list. If a placeholder was wrapped in an SDT, it was "hidden" inside a nested container that the engine wasn't looking into.

## Change summary
- **Recursive Run Discovery**: Replaced the flat loop in `StandardParagraph` with a recursive `add` method that traverses nested containers.
- **SDT Support**: Explicitly added handling for `SdtRun` elements, allowing the engine to "see" inside Word's Form Controls.
- **JAXBElement Unwrapping**: Integrated transparent unwrapping of `JAXBElement` objects, which DOCX4J often uses as containers for runs and other document fragments.
- **Unified Indexing**: Maintained precise `startIndex` and `endIndex` calculations across the nested structure, ensuring that `Paragraph.replace` continues to work with surgical precision.

## Why Recursion?
While I could have added more `if/else` checks to the existing loop, WordprocessingML technically allows for unbounded nesting. A placeholder might be inside a Run, which is inside an SDT, which might itself be wrapped in another JAXB container. 

Recursion is the natural architectural fit for tree-like structures. It allows the engine to "peel the onion" layer by layer until it reaches the actual `R` (Run) elements where the text lives, regardless of how many wrappers Word has decided to put around it.

```java
private int add(int index, Object object) {
    if (object instanceof R r)
        return add(r, index);
    else if (object instanceof SdtRun sdtRun)
        return add(index, sdtRun);
    else if (object instanceof JAXBElement<?> jaxbElement)
        return add(index, jaxbElement.getValue());
    else
        return index + 1;
}
```

## Impact: Embracing the Ecosystem
The most immediate win is the ability to fill Word Forms automatically. This isn't just a technical convenience; it allows users to leverage the vast library of templates available on **office.com**. 

These professional templates heavily rely on Form Controls for placeholders. With this change, you can take a standard office.com template, add your SpEL expressions into the existing form controls, and OfficeStamper will handle it seamlessly. It reduces the "friction" of template creation, letting users work with Word's native features rather than fighting against them.

## Next steps
- **Refining Characterization Tests**: I'll be adding more complex nested templates to the test suite to ensure no regressions as we continue to optimize the traversal engine.

---
*Referenced Issue:* [#286](https://github.com/verronpro/office-stamper/issues/286)
*Referenced Commits:* `3a78067` (StandardParagraph recursion), `e21fd9e` (IndexedRun optimizations).
