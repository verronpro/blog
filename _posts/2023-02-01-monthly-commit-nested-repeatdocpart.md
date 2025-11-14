---
layout: article
title: "Solving Nested Repeats in Word Templates"
date: 2023-02-01 09:00:00
categories: [ wordprocessingml, agility, craftsmanship, docs-as-code, solo-maintainer, enterprise, maintenance repetition ]
---

TL;DR: office-stamper now supports arbitrary nesting depth for repeated document sections. A 13-line recursive fix lets
templates mirror complex domain models (school → grades → classes → students) without data flattening. Includes
comprehensive tests and works with existing templates.

Commit: [0a774c1](https://github.com/verronpro/office-stamper/commit/0a774c1)

## Why this stands out

Imagine generating a school report where each grade needs a list of classes, and each class needs a table of students.
Before this commit, office-stamper would silently break at the second level of nesting.

```plantuml
note as n
School
|_ Grade 1
  |_ Class A
    |_ Student 1
    |_ Student 2
    |_ ...
  |_ Class B 
    |_ Student 3
    |_ Student 4
    |_ ...
|_ Grade 2
  |_ Class C
    |_ ...
end note
```

`repeatDocPart` would fail when comment wrappers nested—the processor couldn't correctly extract child comments into the
sub-template. The processor would extract only the immediate child comment, orphaning deeper comments and causing
rendering to skip nested levels entirely.

This commit introduces recursive comment extraction, unlocking true hierarchical document generation.

From an Agile perspective, this delivers a complete vertical slice: the feature works for arbitrary nesting depth, comes
with a comprehensive multi-level test (school → grade → class → student), and required only a 13-line change to
production code. That's high leverage.

## What changed

The core change is in `RepeatDocPartProcessor.extractSubTemplate()`:

**Before:** Only direct children of a comment wrapper were extracted:

```java
private void extractedSubComments(CommentWrapper commentWrapper, Comments comments) {
    commentWrapper
            .getChildren()
            .forEach(comment -> comments.getComment()
                                        .add(comment.getComment()));
}
```

**After:** Recursively extract all descendant comments:

```java
private void extractedSubComments(CommentWrapper commentWrapper, Comments comments) {
    for (CommentWrapper child : commentWrapper.getChildren()) {
        comments.getComment()
                .add(child.getComment());
        if (CollectionUtils.isEmpty(child.getChildren())) {
            continue;
        }
        extractedSubComments(child, comments); // Recurse
    }
}
```

This depth-first traversal ensures that when a sub-template is stamped, it contains all the comment metadata it needs to
process its own nested repeats.

## The test tells the story

The `RepeatDocPartNestingTest` exemplifies test-as-specification. It models a realistic hierarchy:

- School contains 3 grades
- Each Grade contains 3 classes
- Each Class contains 5 students
- Students are rendered in a table with number, name, and age columns
  The test walks the generated document and validates:
- Correct paragraph counts at each level
- Proper text interpolation ("Grade No.2 there are 3 classes")
- Table structure preservation (3 cells per student row)
  This isn't a toy example; it mirrors real enterprise scenarios like org charts, BOM hierarchies, or multi-level
  approval matrices.

## Agile/craftsmanship/docs-as-code lens

* *Small batches, big impact:* The production change is 13 lines. The test infrastructure (context classes, fixtures) is
  larger, but that's the right ratio—tests define the contract and protect against regression.

* *Recursive simplicity:* Instead of managing explicit stacks or breadth-first queues, the code leverages a natural
  recursion. The call stack is the traversal state. This aligns with the WordprocessingML structure (comments form a
  tree) and keeps the cognitive load low.

* *Documentation through fixtures:* The RepeatDocPartNestingTest.docx template itself is documentation. Future
  maintainers can open it in Word, see the comment structure, and understand the feature without parsing code.

## Solo maintainer + enterprise usage angle

For a solo maintainer, recursion is a double-edged sword—it is elegant but can hide stack-overflow risks. The mitigation
here is *structural*: WordprocessingML comments have finite depth (typically 2–4 levels in practice), and the Spring
`CollectionUtils.isEmpty()` guard prevents infinite loops on malformed trees.

For enterprise users, this removes a major adoption blocker. Before, nested repeats required flattening the data model
or splitting templates. Now, the document structure can mirror the domain model directly. That reduces impedance
mismatch between business stakeholders (who think hierarchically) and template authors.

## Risks and mitigations

### Risk: Stack overflow on pathological templates with deeply nested comments.

*Mitigation:* WordprocessingML comments rarely exceed 5–6 levels; modern JVMs handle this easily. Consider adding a
depth guard if telemetry shows abuse.

### Risk: Comment extraction order matters if processors have side effects.

*Mitigation:* The depth-first traversal is deterministic and matches document order. Side effects should be avoided in
processors, but if needed, they'll fire predictably.

### Risk: Performance on wide trees (e.g., 100 children per level).

*Mitigation:* The recursion is linear in the number of comments. Even 1000 nested comments will complete in
milliseconds.

## Quick Start: Nested Repeats in 3 Steps

**Step 1: Nest Your Comments in Word**
Open your template, add a comment around the outer loop, then add child comments for inner loops. Name them
descriptively (`repeatGrade`, `repeatClass`).

**Step 2: Structure Your Context Object**

```java 
record School(List grades) {}

record Grade(int number, List classes) {}

record AClass(String name, List students) {}
```

**Step 3: Stamp It**

```java
stamper.stampAndLoad(template, new School(grades));
```

## References

- Commit: [0a774c1](https://github.com/verronpro/office-stamper/commit/0a774c1)
-

Issue: [When commentWrapper's children comments have their own children · Issue #92 · verronpro/office-stamper.](https://github.com/verronpro/office-stamper/issues/92)
-
PR: [Nesting support for repeatDocPart commentWrapper #92 by NoNException · Pull Request #100 · verronpro/office-stamper.](https://github.com/verronpro/office-stamper/pull/100)

- Contributors: [NoNException](https://github.com/NoNException), [zongzi](https://github.com/zongzi)
