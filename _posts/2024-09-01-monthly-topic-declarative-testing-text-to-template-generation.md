---
layout: article
title: "Declarative Testing: Text-to-Template Generation"
date: 2024-09-01
categories: [ office-stamper ]
tags: [ testing, docs-as-code, office-stamper, java, wordprocessingml ]
author: Joseph
description: "Reducing friction in document automation testing by replacing binary .docx artifacts with a textual DSL for template generation."
toc: true
---

In the world of **Office-stamper**, testing has always been a heavy-duty affair. To verify a single edge case—say, how the engine preserves a tabulation character inside a run—you traditionally had to:
1. Open Microsoft Word.
2. Create a new document.
3. Carefully type your expression.
4. Save it as a `.docx` file in `src/test/resources`.
5. Link it in your Java test suite.

This "binary friction" is a productivity killer. For a solo maintainer, it means a simple bug report involving a minor formatting issue requires a context switch to a GUI tool. Moreover, these binary blobs are opaque on GitHub; you can't "read" a `.docx` in a Pull Request to understand what the test is actually exercising.

To solve this, I've introduced a **Declarative Testing** approach: generating minimal, specialized Word templates directly from textual input within the test code.

## The Technique — `makeResource`: From String to Stamping

The heart of this change is a utility method called `makeResource`. Instead of loading a file from disk, it takes a `String` and uses **DOCX4J** to build a valid `WordprocessingMLPackage` on the fly.

This allows us to write tests that are entirely self-contained. The "template" lives right next to the "assertion".

```java
private static InputStream makeResource(String content) throws Docx4JException {
    var aPackage = WordprocessingMLPackage.createPackage();
    var mainDocumentPart = aPackage.getMainDocumentPart();
    
    content.lines().forEach(line -> {
        var split = line.split("\\|TAB\\|");
        var run = initRun(split[0]);
        
        for (int i = 1; i < split.length; i++) {
            run.getContent().add(new R.Tab());
            var text = new Text();
            text.setValue(split[i]);
            run.getContent().add(text);
        }
        
        var p = new P();
        p.getContent().add(run);
        mainDocumentPart.addObject(p);
    });

    var outputStream = new ByteArrayOutputStream();
    aPackage.save(outputStream);
    return new ByteArrayInputStream(outputStream.toByteArray());
}
```

## Worked Examples (The DSL in Action)

By using a simple delimiter like `|TAB|`, we can exercise specific engine behaviors without the overhead of external files.

### 1. Tabulation Preservation
We want to ensure that `OfficeStamper` doesn't "eat" tabs when it replaces placeholders.

```java
@ParameterizedTest
void tabulationTest() {
    InputStream template = makeResource("""
        Name:|TAB|${name}
        City:|TAB|${city}
        """);
        
    // Stamp and verify...
}
```

### 2. Multi-line Expressions
Testing how the engine handles line breaks becomes trivial:

```java
InputStream template = makeResource("""
    First Line
    Second Line with ${placeholder}
    """);
```

## Why this works (The Philosophical Fit)

This approach shifts the **mental model** of the project from "Library + External Assets" to "Self-Documenting Code". 

- **GitHub-Centricity**: You can read the test and understand the use case intuitively on GitHub. No need to download binary artifacts and keep the Java context in your head.
- **Surgical Precision**: You only generate exactly what you need to test. No "formatting noise" from Word's default styles or metadata.
- **Lower Contribution Barrier**: A contributor can fix a bug and add a regression test without even having Microsoft Office installed.

## Future — Towards an AsciiDoc-inspired DSL

The current implementation is purposefully minimal, but I see it evolving. Inspired by the feature set of **AsciiDoc**, I intend to let this DSL grow to handle:
- **Tables**: Using a `|===` syntax to define rows and cells.
- **Sections**: Handling headers and footers (leveraging the new `DocxPart` refactor).
- **Styling**: Inline markers for bold, italic, or specific Word styles.

The goal is to eventually handle most simple Word document structures through this declarative interface, keeping the binary templates reserved only for high-fidelity "look and feel" integration tests.

## Checklist — Moving to Declarative Templates

1. **Identify "Surgical" Tests**: Look for tests that only care about structure or text, not complex layouts.
2. **Abstract the Package Creation**: Use a helper like `makeResource` to hide the DOCX4J boilerplate.
3. **Define a Minimal DSL**: Start with simple markers (like `|TAB|`) that map to WordprocessingML elements.
4. **Co-locate Template and Assertion**: Keep the input string in the same file as the test logic.
5. **Reserve Binaries for High-Fidelity**: Use `.docx` files only when testing complex interactions between many Word features (e.g., Charts + Tables + Sections).

By letting the code describe the document, we make the engine's behavior transparent, documented, and—most importantly—frictionless.
