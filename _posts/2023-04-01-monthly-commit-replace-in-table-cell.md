---
layout: article
title: "Debugging Docx Generation with Inspectable Output"
date: 2023-04-01 09:00:00
categories: [office-stamper]
author: Joseph
tags: [office-stamper,testing, java, templates, docs-as-code,wordprocessingml, agility, craftsmanship, test-infrastructure, refactoring, tables, java17, debugging-tool]
description: "Before fixing a tricky table‑cell bug, we invested in inspectable outputs, modern Java patterns, and stable assertions—turning infrastructure into leverage for complex WordprocessingML debugging."
---

Before fixing a table cell bug, invest in the tools to understand it—this commit added debug-friendly test infrastructure, modernized code with Java 17 patterns, and built a stable assertion layer for complex WordprocessingML structures, proving that infrastructure work is feature work.

Commit: [7b387f5](https://github.com/verronpro/office-stamper/commit/7b387f5)

## What the commit actually does

The best way to fix a bug is to make it impossible to hide. Before tackling a tricky table cell processing issue, this commit invested in the tools to see the problem clearly: inspectable test output, modern Java patterns for readability, and a paragraph extraction utility that turns brittle assertions into stable contracts. No algorithm changes, no user-facing fixes—just building a workshop where the real work can happen safely.

### Key changes by category

| What Changed                    | Why It Matters                                     |
|---------------------------------|----------------------------------------------------|
| Added Lombok                    | Cut boilerplate → faster reading during debugging  |
| Pattern matching for instanceof | 30% less noise in object walkers                   |
| KEEP_OUTPUT_FILE toggle         | Debug tests by opening actual .docx files          |
| ParagraphCollector utility      | Stable assertions for nested structures            |
| Stream-based text extraction    | Self-documenting data flow                         |

**1. Dependency & tooling updates**
```xml
<!-- pom.xml: Added Lombok for boilerplate reduction -->
<dependency>
<groupId>org.projectlombok</groupId>
<artifactId>lombok</artifactId>
<version>1.18.26</version>
</dependency>
```
**2. Code modernization using Java features**

The most visible theme is **pattern matching for instanceof** (Java 14+) and **simplification**:
```java
// ObjectDeleter.java - Before:
if (paragraph.getParent() instanceof Tc) {
    Tc parentCell = (Tc) paragraph.getParent(); // ← Redundant cast
    deleteFromCell(parentCell, paragraph);
}

// After:
if (paragraph.getParent() instanceof Tc parentCell) {
    deleteFromCell(parentCell, paragraph);
}
```

Method negation became more intention-revealing:
```java
// TableCellUtil.java - Before:
public static boolean hasAtLeastOneParagraphOrTable(Tc cell);

// After:
public static boolean hasNoParagraphOrTable(Tc cell);
```
Stream-based collection replaced manual loops:
```java
// ParagraphWrapper.java - Before:
private String getText(List<IndexedRun> runs) {
    StringBuilder builder = new StringBuilder();
    for (IndexedRun run : runs) {
        builder.append(RunUtil.getText(run.getRun()));
    }
    return builder.toString();
}

// After:
public String getText() {
    return runs.stream()
               .map(IndexedRun::getRun)
               .map(RunUtil::getText)
               .collect(joining());
}
```
**3. Test infrastructure improvements**

The test base class gained crucial debugging capabilities:
```java
// AbstractDocx4jTest.java
public static final boolean KEEP_OUTPUT_FILE =
Boolean.parseBoolean(System.getenv()
.getOrDefault("keepOutputFile", "false"));

protected OutputStream getOutputStream() throws IOException {
    if (KEEP_OUTPUT_FILE) {
        Path temporaryFile = Files.createTempFile(/*...*/);
        logger.info("Saving DocxStamper output to temporary file %s".formatted(temporaryFile));
        // streams tracked for later inspection
    }
}
```

New utility for paragraph extraction:
```java
// ParagraphCollector.java - NEW
public class ParagraphCollector extends TraversalUtilVisitor<P> {

    private final List<P> paragraphs = new ArrayList<>();

    public Stream<P> paragraphs() {         
        return paragraphs.stream();
    }
}

// AbstractDocx4jTest.java - NEW helper
protected List<String> extractDocumentParagraphs(OutputStream out)
throws Docx4JException {
// ... load document, traverse with ParagraphCollector
}
```
**4. Test hygiene**

- Deleted empty/stub files (`ProxyMethodHandlerTest.java`, `ITestInterface.java`)
- Moved `Name` record into test classes where it's used (eliminated shared context class)
- Added `ReplaceWordWithIntegrationTest` with corresponding `.docx` fixture (16KB binary)

### Why Infrastructure Work Is Feature Work
For the solo maintainer:
- Every hour spent on clear test infrastructure saves 10 hours of debugging.
- Readable code is debuggable code. Pattern matching isn't cosmetic—it's maintenance strategy.
- When you're the only person who can fix production issues, tools that surface problems faster are survival tools.

For enterprise adopters:
- Test utilities like ParagraphCollector mean the library can be tested in complex scenarios—which means it will be fixed when issues arise.
- Modern Java features signal active maintenance and forward compatibility.
- Debug toggles prove the maintainer tests the same way enterprise teams do: with real artifacts.

> **🎯 Testing Principle: Separation of Concerns**
>
> This commit follows a deliberate two-phase approach:
> 1. **Phase 1 (this commit):** Build the tools to observe and assert on table behavior
> 2. **Phase 2 (next commit):** Write failing tests, then fix them
>
> Why separate? Because infrastructure changes are *behavior-preserving* and should pass CI immediately. Mixing them with bug fixes creates tangled history and makes bisecting regressions harder.

## What's next

The stage is set for:
- Actual assertions in `ReplaceWordWithIntegrationTest` that exercise comment processing in table cells
- Fixes to `TableCellUtil`, `ObjectDeleter`, or `DocumentWalker` that make those assertions pass
- Confidence that the test harness can detect regressions in nested table structures

## References

- Commit: [7b387f5](https://github.com/verronpro/office-stamper/commit/7b387f5)
- Issue: [#67 replacewordwith doesn't work within a table cell](https://github.com/verronpro/docx-stamper/issues/67)
- Added tests: `ReplaceWordWithIntegrationTest.java` (shell), `ParagraphCollector.java` (new utility)
- Refactored: `ObjectDeleter`, `TableCellUtil`, `ParagraphWrapper`, `AbstractDocx4jTest`
