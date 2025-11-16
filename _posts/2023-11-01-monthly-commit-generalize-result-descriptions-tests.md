---
layout: article
title: "Make Tests Tell a Story: Generalizing Result Descriptions"
date: 2023-11-01
categories: [ office-stamper ]
tags: [ office-stamper,testing, craftsmanship, agility, docs-as-code, solo-maintainer, enterprise, readability, maintainability ]
author: Joseph
description: Centralized test assertion descriptions to make failures readable, replacing brittle object dumps with domain-meaningful narratives that accelerate debugging and onboard contributors.
---

**TL;DR:** Centralized test result descriptions across 157 test cases, replacing ad-hoc assertions with a shared vocabulary. Tests now read like documentation and failures explain *what broke* in business terms, not XML structures.

Commit: [7afd857](https://github.com/verronpro/office-stamper/commit/7afd857)

## Why this is October's standout

When projects are maintained by one person and used sporadically inside
big companies, tests are not only a safety net—they are one of the main
ways new contributors learn how the system behaves. In October 2023 I
generalized the string‑based description of test results to increase
readability and intent. It sounds small, but the impact is real:
reviewers understand failures faster, contributors spend less time
deciphering assertion details, and future me can triage a red CI job
without mentally reconstructing the expected state from raw object
graphs.

Tests often fail "usefully" when the failure message explains what the
system intended to do in domain words, not in low‑level structures.
Instead of a brittle dump of runs, fields, and comment markers, a
generalized description routine can say: "expected one placeholder
replaced in paragraph 3, found none," or "expected no comment ranges in
SDT X, found 1." That is a better conversation starter in code reviews
and issue triage.

## What actually changed

- **Unified test data model** by removing duplicate context classes (`Name.java`, `NamesContext.java`) and consolidating them into a single `Contexts.names()` factory method that returns inline records—less boilerplate, more focus on test intent.

- **Rewrote 3 major test files** (`DefaultTests.java`, `MultiStampTest.java`, test utilities) to use the generalized `stampAndLoadAndExtract()` pattern, replacing 50+ lines of manual DOM traversal and brittle cell-content assertions with simple string equality.

- **Standardized assertion format** across 157 parameterized tests using `arguments()` instead of `of()`, ensuring consistent parameter naming and making test data more scannable.

- **Converted DOM inspection to domain assertions** in `MultiStampTest`: instead of counting table rows and cells, the test now compares a readable multi-line string showing the exact expected document structure—failures highlight the *semantic* delta, not XML noise.

- **Improved code formatting** in `RepeatDocPartProcessor.java` (254 line changes, mostly indentation consistency) to match the rest of the codebase, making future diffs cleaner.

These improvements make tests read like documentation‑as‑code. When
someone opens a test to understand how `Paragraph.replace` behaves or
how comments are removed from `SdtRun`, the narrative is easier to
follow. It also helps keep PRs focused: when assertion messages are
clear, reviewers can debate intent rather than re‑derive it from
low‑level details.

**Example transformation:**

```java
// Before: brittle DOM traversal
assertRowContainsText(tableRows.get(0), "Homer");
assertTrue(cellContent.contains(text), 
    String.format("'%s' is not in '%s'", text, cellContent));

// After: readable narrative assertion
assertEquals("""
    This table row should be expanded to multiple rows: Homer.
    This table row should be expanded to multiple rows: Marge.
    ...""", stamper.stampAndLoadAndExtract(template, context));
```
## Agile and craftsmanship lens
- **Optimize for feedback clarity.** A crisp failure message is a rapid feedback loop; it tells you what to fix and where. The new format shows *expected vs actual* as readable paragraphs, not object graphs.
- **DRY the narrative.** A single description engine (`stampAndLoadAndExtract()`) avoids tiny divergences across tests that confuse future readers. The 157 test cases now share one vocabulary.
- **Test as teaching.** When tests read like a short story about the behavior, they onboard contributors and document design. The `MultiStampTest` example now shows *exactly* what multi-stamping produces.
- **Reduce test maintenance tax.** By centralizing context creation (`Contexts.names()`) and output formatting, test refactoring becomes surgical—change one method, fix all 157 tests.

## Solo‑maintainer + enterprise usage angle
As the sole maintainer, I rely on tests to double as living documentation. When an enterprise team reports a bug, I often encode their scenario as a fixture and a test. If that test's failure explains the mismatch in domain terms, we can align faster—sometimes asynchronously, without meetings. For contributors dropping in every few months, a consistent assertion vocabulary lowers cognitive load: they can pattern‑match on familiar phrasing and focus on the change at hand.

The `MultiStampTest` rewrite is a perfect example: the old version required understanding DOCX4J's `Tbl`, `Tr`, `Tc` hierarchy and `TextUtils.getText()` semantics. The new version just shows the expected document as a readable string—anyone can verify correctness without DOCX4J knowledge.

## Risks and mitigations
- **Risk:** Over‑abstracting failure details loses debugging context<br>
**Mitigation:** The `stampAndLoadAndExtract()` output includes formatting markers (like `❬text❘properties❭`) that preserve structural information while staying human-readable. Deep debugging can still inspect the raw `WordprocessingMLPackage`.

- **Risk:** Description drift vs. code—test assertions claim behavior X but implementation does Y.<br>
**Mitigation:** Descriptions are built from the same domain primitives the engine uses (`DocumentUtil`, paragraph extraction), so they evolve together. The 157 parameterized tests act as a regression suite.

- **Risk:** Hard‑to‑read diffs when tests fail.<br>
**Mitigation:** Multi-line strings with consistent structure (one paragraph per line) produce line-by-line diffs highlighting which paragraph changed.

- **Risk:** Test data duplication when multiple tests need similar contexts.<br>
**Mitigation:** The `Contexts` factory class centralizes creation patterns (`names()`, `role()`, `empty()`), making test setup DRY and discoverable.

## How to apply this in your project
1. **Write a "result description" helper** that takes your domain objects and renders meaningful summaries. For DOCX, this meant extracting text + formatting markers; for your domain, it might be JSON schemas, HTTP response shapes, or state machine transitions.
2. **Replace bespoke assertion messages** with calls to this helper across your test suite. Start with your most-changed tests to prove value, then refactor systematically.
3. **Consolidate test data builders** into a single factory class (like `Contexts`). This reduces duplication and makes test setup patterns discoverable.
4. **Standardize your test structure:** use the same test method signature, parameter order, and naming conventions. The 157-test suite uses `(name, config, context, template, expected)` everywhere—muscle memory for readers.
5. **Include example failure messages in your docs** so contributors know what to expect from CI. Show the *before* (raw object graph) and *after* (domain narrative) to illustrate the value.

## References
- Commit: [7afd857](https://github.com/verronpro/office-stamper/commit/7afd857)
- Test suite refactoring: `DefaultTests.java` (157 parameterized tests), `MultiStampTest.java` (DOM→string conversion)
- Context consolidation: removed `Name.java`, `NamesContext.java`; added `Contexts.names()` factory
- Related commits: tightening test setup and generalized descriptions around 2023‑10‑16 and 2023‑10‑25.
