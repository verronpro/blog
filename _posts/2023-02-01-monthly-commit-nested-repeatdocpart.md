---
layout: article
title:  "Nested Repeats in Word Templates: Making repeatDocPart Robust"
date:   2023-02-01 09:00:00
categories: [agility, craftsmanship, docs-as-code, solo-maintainer, enterprise, maintenance, wordprocessingml, repetition]
---

Commit: [0a774c1](https://github.com/verronpro/office-stamper/commit/0a774c1)

## Why this stands out

Enterprise templates frequently demand nested repetition: render a table of students inside each class, classes inside each grade, grades inside a school. Before this commit, `repeatDocPart` would fail when comment wrappers nested—the processor couldn't correctly extract child comments into the sub-template. This commit introduces recursive comment extraction, unlocking true hierarchical document generation.

From an Agile perspective, this delivers a complete vertical slice: the feature works for arbitrary nesting depth, comes with a comprehensive multi-level test (school → grade → class → student), and required only a 13-line change to production code. That's high leverage.

## What changed

The core change is in `RepeatDocPartProcessor.extractSubTemplate()`:

**Before:** Only direct children of a comment wrapper were extracted:

```java
private void extractedSubComments(CommentWrapper commentWrapper, Comments comments) {
    commentWrapper
            .getChildren()
            .forEach(comment -> comments.getComment().add(comment.getComment()));
}
```

**After:** Recursively extract all descendant comments:

```java
private void extractedSubComments(CommentWrapper commentWrapper, Comments comments) {
    for (CommentWrapper child : commentWrapper.getChildren()) {
        comments.getComment().add(child.getComment());
        if (CollectionUtils.isEmpty(child.getChildren())) {
            continue;
        }
        extractedSubComments(child, comments); // Recurse
    }
}
```

This depth-first traversal ensures that when a sub-template is stamped, it contains all the comment metadata it needs to process its own nested repeats.

## The test tells the story
`RepeatDocPartNestingTest` is a good example in test-as-specification. It models a realistic hierarchy:
 
 - School contains 3 grades
 - Each Grade contains 3 classes
 - Each Class contains 5 students
 - Students are rendered in a table with number, name, and age columns
The test walks the generated document and validates:
 - Correct paragraph counts at each level
 - Proper text interpolation ("Grade No.2 there are 3 classes")
 - Table structure preservation (3 cells per student row)
This isn't a toy example—it mirrors real enterprise scenarios like org charts, BOM hierarchies, or multi-level approval matrices.

## Agile/craftsmanship/docs-as-code lens
 * Small batches, big impact:* The production change is 13 lines. The test infrastructure (context classes, fixtures) is larger, but that's the right ratio—tests define the contract and protect against regression.

 * Recursive simplicity:* Instead of managing explicit stacks or breadth-first queues, the code leverages a natural recursion. The call stack is the traversal state. This aligns with the WordprocessingML structure (comments form a tree) and keeps the cognitive load low.

 * Documentation through fixtures:* The RepeatDocPartNestingTest.docx template itself is documentation. Future maintainers can open it in Word, see the comment structure, and understand the feature without parsing code.

## Solo maintainer + enterprise usage angle

For a solo maintainer, recursion is a double-edged sword—it is elegant but can hide stack-overflow risks. The mitigation here is *structural*: WordprocessingML comments have finite depth (typically 2–4 levels in practice), and the Spring `CollectionUtils.isEmpty()` guard prevents infinite loops on malformed trees.

For enterprise users, this removes a major adoption blocker. Before, nested repeats required flattening the data model or splitting templates. Now, the document structure can mirror the domain model directly. That reduces impedance mismatch between business stakeholders (who think hierarchically) and template authors.

## Risks and mitigations

### Risk: Stack overflow on pathological templates with deeply nested comments.
*Mitigation:* WordprocessingML comments rarely exceed 5–6 levels; modern JVMs handle this easily. Consider adding a depth guard if telemetry shows abuse.

### Risk: Comment extraction order matters if processors have side effects.
*Mitigation:* The depth-first traversal is deterministic and matches document order. Side effects should be avoided in processors, but if needed, they'll fire predictably.

### Risk: Performance on wide trees (e.g., 100 children per level).
*Mitigation:* The recursion is linear in the number of comments. Even 1000 nested comments will complete in milliseconds.

## How to apply this

1. Structure your template: Use nested Word comments to delimit repeated regions. Outer comment wraps the container, inner comments wrap the items.
2. Match your data model: Pass a context object with matching hierarchy (e.g., `SchoolContext.getGrades()` → `Grade.getClasses()` → `AClass.getStudents()`).
3. Leverage expression language: Use placeholder expressions like `${grade.number}` inside repeated regions—they resolve in the correct scope.
4. Test incrementally: Start with one level of nesting, validate, then add the next level.

## References

- Commit: [0a774c1](https://github.com/verronpro/office-stamper/commit/0a774c1)
- Issue: [When commentWrapper's children comments have their own children · Issue #92 · verronpro/office-stamper.](https://github.com/verronpro/office-stamper/issues/92)
- PR: [Nesting support for repeatDocPart commentWrapper #92 by NoNException · Pull Request #100 · verronpro/office-stamper.](https://github.com/verronpro/office-stamper/pull/100)
- Contributors: [NoNException](https://github.com/NoNException), [zongzi](https://github.com/zongzi)
