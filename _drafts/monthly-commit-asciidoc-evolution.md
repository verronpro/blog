---
layout: article
title: "AsciiDoc Evolution: From Regex to AST"
date: 2025-12-22
categories: [ office-stamper ]
tags: [ office-stamper, java, asciidoc, refactoring ]
author: Joseph
description: Moving to a robust AST-based parser for the AsciiDoc module to support complex structures like tables and tab stops in Word.
---

For a while, `office-stamper` has supported a minimal subset of AsciiDoc through a dedicated extension. It was powered by a custom-built, regex-based parser. While this worked for simple headings and paragraphs, it was essentially a "dead end" for more complex document structures. 

As we pushed towards the v3 milestone, I realized that technical documentation in Word shouldn't be limited to flat text. Users need tables, precise alignment, and reliable parsing that doesn't break when a paragraph has a slightly unusual character.

## The Problem: The Regex Ceiling

Regex-based parsing is great for "docs-as-code" early stages, but it fails to capture the recursive nature of document blocks. How do you handle a table inside a section? Or bold text inside a table cell? My homemade parser was becoming a collection of edge cases and `if-else` blocks.

## The Solution: AsciidoctorJ AST

This month, I've completely overhauled the `asciidoc` module by integrating `AsciidoctorJ`. Instead of trying to "guess" the structure of the text, we now let the official engine build a real Abstract Syntax Tree (AST), which we then traverse to build our internal `AsciiDocModel`.

### 1. Structural Mapping
We now have a first-class representation of **Tables**, **Rows**, and **Cells**. These map directly to the corresponding WordprocessingML elements (`<w:tbl>`, `<w:tr>`, `<w:tc>`).

### 2. The Reflection Bridge
The `AsciidoctorJ` API can sometimes be tricky to navigate across different environments or when dealing with its internal JRuby bridge. To ensure the table support is robust for users, I've used a "pragmatic reflection" approach to extract rows.

```java
private static List<AsciiDocModel.Row> extractTableRowsViaReflection(StructuralNode tableNode) {
    List<AsciiDocModel.Row> out = new ArrayList<>();
    try {
        Object content = tableNode.getContent();
        if (content != null) {
            Object rowsObj = invokeNoArg(content, "getRows");
            // ... extracts Head and Body rows via reflection ...
        }
    } catch (Throwable ignore) {
        // Fallback: best-effort traversal of children nodes
    }
    return out;
}
```

This is a classic "solo maintainer" trade-off. While I prefer strong types, I'd rather have a slightly "dirty" reflection-based bridge that works across minor library versions than a "pure" one that breaks because of a minor API change in a transitive dependency.

### 3. Precision with the `|TAB|` Token
One of the sharp edges of mapping AsciiDoc to Word is the concept of a tab stop. AsciiDoc doesn't have a native "tab" character that maps cleanly to Word's `<w:tab/>`. 

I've introduced a simple, pragmatic token: `|TAB|`. When the parser sees this in a line of text, it flushes the current text run and inserts a `Tab` element into our model.

## Step-by-Step Migration

The migration from regex to `AsciidoctorJ` followed a strict path to ensure no regression:

1.  **Dependency Injection**: Added `asciidoctorj` to the `asciidoc` extension module.
2.  **Model Alignment**: Redefined the `AsciiDocModel` to align with the AST nodes (Sections, Paragraphs, Tables).
3.  **The Visitor Pattern**: Implemented a recursive walker that visits `StructuralNode` objects and builds the model.
4.  **Test Harmonization**: Updated existing tests. Because we now use a real parser, the input text in our tests is much closer to valid, standard AsciiDoc than the "pseudo-Asciidoc" my regex parser required.

## Sharp Edges: The JRuby Tax

While `AsciidoctorJ` is powerful, it comes with a significant "sharp edge": it relies on JRuby. For a lightweight library like `office-stamper`, the overhead of loading the JRuby runtime is non-negligible. It adds a few seconds to the first stamp operation, which might be a dealbreaker for some CLI usages.

## Impact: Accurate Technical Reports

By moving to a real AST, we've gained:
*   **Structural Fidelity**: Tables in AsciiDoc now render as native Word tables, supporting automatic column fitting and multi-line cell content.
*   **Whitespace Preservation**: By explicitly setting `tx.setSpace("preserve")` in our DOCX emitter, we've solved the issue of Word stripping leading or trailing spaces—critical for technical snippets.
*   **Standard Compliance**: The parser is now "correct" by definition, as it uses the official implementation.

## Next Steps: Towards a Pure Java Parser

I'm not stopping here. To solve the JRuby performance issue, I'm planning to move to a **pure Java AsciiDoc parser** in the future—even if I have to contribute significantly to one or build a focused subset myself. 

I've recently subscribed to the **Apache group** working on this topic to gather feedback and see how the community is approaching a native Java implementation. Until then, `AsciidoctorJ` provides the structural reliability we need for v3.

---

### December 22nd 2025 — Commit Summary:
- **Feat**: Migrated to `AsciidoctorJ` AST for robust AsciiDoc parsing.
- **Feat**: Added structural support for Tables, Rows, and Cells in DOCX output.
- **Feat**: Implemented `|TAB|` token for native DOCX tab stops.
- **Refactor**: Reflection-based fallback for table extraction to improve environment compatibility.
- **Fix**: Enabled XML space preservation in DOCX text runs to prevent trimming.
- **Perf**: Acknowledged JRuby startup overhead; initiated search for pure Java alternative via Apache community.
