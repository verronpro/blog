---
layout: article
title: "Extending SpEL: How to Register Custom Functions in Office-stamper"
date: 2024-10-31
categories: [office-stamper]
author: Joseph
tags: [agility, craftsmanship, solo-maintainer, enterprise, platform, ci-cd, risk-management, spel, extensibility]
description: "A step-by-step guide to safely extending expressions with custom functions and suppliers in the SpEL resolver."
---

## Why extend SpEL?

While Spring Expression Language (SpEL) is powerful out of the box, complex document logic can often lead to long, unreadable expressions in your templates. By registering custom functions and suppliers, you can move that complexity behind intention-revealing names, creating a "pit of success" for template authors.

## Prerequisites

- `office-stamper` version 2.1.0 or higher.
- A Java project using `DocxStamper`.

## Step-by-Step Guide

### 1. Identify high-value helpers
Start with helpers that solve common formatting or logic problems:
- Date formatting (e.g., `${formatDate(date)}`)
- Currency conversion
- Safe wrappers for potentially null fields

### 2. Define the functions
Create a class with static methods that you want to expose as functions. Keep these functions pure and deterministic to avoid side effects during stamping.

```java
public class DocumentFunctions {
    public static String formatDate(LocalDate date) {
        return date.format(DateTimeFormatter.ofPattern("dd MMMM yyyy"));
    }
}
```

### 3. Register via Configuration
Use the `DocxStamperConfiguration` to register your functions. You can use namespacing to avoid collisions and improve governance.

```java
DocxStamperConfiguration config = new DocxStamperConfiguration()
    .exposeFunction("f", DocumentFunctions.class.getMethod("formatDate", LocalDate.class));
```

### 4. Use in Templates
In your Word document, you can now call the function using the `#` prefix:
`${formatDate(invoice.date)}`

## Verification

To ensure your functions are working as expected:
1.  **Unit Tests**: Add tests that exercise the SpEL invocation through the `DocxStamper`.
2.  **Diagnostics**: Enable `DEBUG` logging for the resolver to see how expressions are being evaluated.

## Troubleshooting & Pitfalls

-   **Sprawl**: Avoid adding a function for every tiny use case. Prune unused functions regularly.
-   **Side Effects**: Never allow functions to modify the context or the document state.
-   **Security**: Always use a whitelist approach. Avoid exposing reflection-based open resolvers that could allow template authors to execute arbitrary code.
-   **Null Behavior**: Explicitly document and test how your functions handle null inputs. Should they return an empty string, a placeholder, or throw an exception?
