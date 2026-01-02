---
layout: article
title: "Predicting the Unpredictable: The v3 Context Overhaul"
date: 2025-12-15
categories: [ office-stamper ]
tags: [ office-stamper, java, architecture, v3 ]
author: Joseph
toc: true
description: A deep dive into the hierarchical context system of office-stamper v3 and how it finally solves the nested loops problem.
---

Yesterday, December 14th, we officially released `office-stamper` v3.0.0. While the version number changed by just one digit, the architectural overhaul under the hood is the most significant since I forked the project.

Today, I want to dive into the core of this release: the move to a **hierarchical context system**.

## The Thesis: Predictability is King

In a template engine, the most "magical" part is often how expressions (SpEL, in our case) find their data. In v2.x, this was a bit of a "flat" affair. While it worked for simple documents, complex templates with nested repeaters often struggled with variable shadowing and scope leakage.

For v3, I had one goal: **make the resolution of expressions strictly predictable**.

## The Technique: `ContextTree` and `ContextBranch`

Instead of a single root object, v3 uses a `ContextTree`. Every time the engine enters a nested scope (like a repeat loop), it pushes a new `ContextBranch` onto a stack.

When an expression like `${item.name}` is evaluated, the engine doesn't just look at the current item. It looks down the stack of branches, from the most specific (the current loop item) to the most general (the document root).

```java
// Conceptual v3 resolution flow
var contextTree = new ContextRoot(contextRoot);
// ... inside a loop ...
var branch = contextTree.push(currentItem);
// SpEL can now see currentItem, AND anything from the outer scopes.
```

## Worked Example: The Nested Loop Problem

Imagine an invoice template where you have a list of `Categories`, and inside each category, a list of `Items`. In v2.x, if you wanted to access the `category.total` from inside the `item` loop, you often had to resort to messy workarounds.

In v3, this is native. The `item` branch is a child of the `category` branch. SpEL naturally resolves the variables by walking up the tree. Shadowing works exactly as you'd expect: if both objects have a `name` property, the `item.name` wins inside the loop, but you can still reach out to the parent if needed.

## EvaluationContextFactory: Fresh State Every Time

To complement the hierarchical context, I've replaced the old `EvaluationContextConfigurer` with an `EvaluationContextFactory`. 

Instead of mutating a shared, long-lived context (which was a source of subtle state bugs), the engine now creates a completely **fresh** `EvaluationContext` for every single comment or placeholder "Hook". 

```java
// v3 Factory pattern
config.setEvaluationContextFactory(root -> {
    var ctx = new StandardEvaluationContext(root);
    // ... custom setup ...
    return ctx;
});
```

This ensures that no side effects from one part of the document can leak into another. It is "clean room" processing for every expression.

## Pitfalls & Migration

The biggest pitfall is, of course, the **breaking change**. Moving to document-order processing and a stack-based context means that some templates might behave differently if they relied on the "accidental" resolution order of v2.x.

As a solo maintainer, I'm concerned about the friction this causes for my users. But for the long-term health of the library—and for my own goals of handling complex business logic—this migration was unavoidable. 

## Checklist for v3 Migration

If you are upgrading this week:
1. [ ] **Update imports**: Core interfaces moved to `pro.verron.officestamper.api`.
2. [ ] **Switch to Factory**: Replace `EvaluationContextConfigurer` with `EvaluationContextFactory`.
3. [ ] **Test Nested Scopes**: Verify your repeaters and conditional comments.
4. [ ] **Check Order**: If you have comments and placeholders on the same line, verify they still resolve in the order you expect.

## Final Thoughts

Shipping v3 is a relief. It is the version of `office-stamper` I've wanted to build since day one. It sheds the technical debt of the original fork and establishes a professional, predictable foundation for the future.

---

### December 15th 2025 — Release Summary:
- **Major**: Released `office-stamper` v3.0.0.
- **Feat**: Implemented hierarchical `ContextTree` for SpEL resolution.
- **API**: Introduced `EvaluationContextFactory` for better isolation.
- **Breaking**: Consolidated public API into `.api` package.
- **Refactor**: Strict document-order processing via `DocxIterator`.
