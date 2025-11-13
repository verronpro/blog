---
layout: article
title: "Building the Foundation: Test Infrastructure and Refactoring for Table Cell Processing"
date: 2023-04-01 09:00:00
categories: [agility, craftsmanship, test-infrastructure, refactoring, wordprocessingml, tables]
---

Commit: [7b387f5](https://github.com/verronpro/office-stamper/commit/7b387f5)

## What the commit actually does

The commit message says "make a non regression test for comment processing in table cells," and that's exactly what the code shows. This isn't a bug fix—it's **preparatory work**: cleaning up technical debt, modernizing the test harness, and setting the stage for safer table operations.

### Key changes by category

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
    Tc parentCell = (Tc) paragraph.getParent();
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
public static boolean hasAtLeastOneParagraphOrTable(Tc cell)

// After:
public static boolean hasNoParagraphOrTable(Tc cell)
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

## Why this matters for a solo maintainer

This commit is invisible to users but **critical for velocity**:

1. **Debugging speed**: `KEEP_OUTPUT_FILE` means you can inspect failed test output in Word without juggling byte arrays.
2. **Readability**: Stream pipelines and pattern matching cut 20–30% of the noise, making defect hunts faster.
3. **Safe refactoring**: The new `ParagraphCollector` and `extractDocumentParagraphs` provide a **stable assertion layer** for table operations. Future changes can verify "these paragraphs still exist in this order" without brittle XPath.

## The craftsmanship angle: refactor infrastructure *before* the fix

Notice what's missing: **no algorithm changes to replacement logic itself**. This commit says "I need better tools to reason about table behavior before I touch the risky code." That discipline prevents scope creep and keeps each commit auditable.

The test file name—`ReplaceWordWithIntegrationTest`—signals the *intent*. The next commit(s) will add assertions that fail against the current buggy behavior, then fix them. This one just proves the harness can load the fixture and extract structure.

## Agile practice: small, shippable infrastructure

Shipping test infrastructure separately has two benefits:

- **Faster review**: changes are mechanical (streams, pattern matching) or purely additive (new utilities). Low cognitive load.
- **Bisect-friendly history**: if a future regression appears, `git bisect` won't flag this commit—it's provably behavior-preserving.

For docs-as-code, the commit message is terse but honest: "make a non regression test." The patch backs that up. No overclaiming.

## How to apply this

1. **Decouple infrastructure from fixes**. Add your `ParagraphCollector` or debug helpers in one commit; land the fix in the next.
2. **Modernize incrementally**. Pattern matching and streams improve readability without changing behavior—safe wins during maintenance windows.
3. **Make output inspectable**. Environment-variable toggles for keeping test artifacts pay off the first time you need to open a document and see what went wrong.

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
