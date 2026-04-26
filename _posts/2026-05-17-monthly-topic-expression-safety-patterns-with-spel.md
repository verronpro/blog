---
layout: article
title: "Monthly Topic — Expression Safety Patterns with Spring SpEL"
date: 2026-05-17
categories: [ office-stamper ]
tags: [ office-stamper, spel, security, docs-as-code ]
author: Joseph
description: A practical playbook for safe templating with Spring SpEL in office‑stamper — patterns, examples, pitfalls, and a checklist.
toc: true
---

In March, I pivoted `office‑stamper` back to **Spring SpEL** to unlock predictable nested contexts and fix long‑standing sharp edges. In April, we delivered SVG stamping. In May, I’m focusing on **safety**: how to let expressions power your templates without opening doors you don’t want opened.

This article is a field guide for integrators and CLI users embedding SpEL in DOCX templates. It shows how to structure data, pick the right **SecurityMode**, and write resilient expressions.

Related issues: [#676]({{site.ghos}}/issues/676), [#677]({{site.ghos}}/issues/677), [#681]({{site.ghos}}/issues/681), [#682]({{site.ghos}}/issues/682).

## 1) Choose the right SecurityMode first

`office‑stamper` is secure‑by‑default. You can switch SpEL between `RESTRICTED` and `PERMISSIVE` explicitly.

```java
import pro.verron.officestamper.api.SecurityMode;
import pro.verron.officestamper.preset.OfficeStamperConfigurations;

var cfg = OfficeStamperConfigurations.standard()
    .setSpelSecurityMode(SecurityMode.RESTRICTED); // default; blocks T(), constructors, etc.

// For trusted admin templates only:
// cfg.setSpelSecurityMode(SecurityMode.PERMISSIVE);
```

Ops can lock this down with `-Dofficestamper.spel.mode=restricted`.

## 2) Shape your data — push logic to Java, not to expressions

- Prefer **DTOs** with precomputed fields over exposing domain entities with rich behavior.
- Provide **booleans and enums** for branching instead of complex method chains.
- Keep collections simple (lists of records or maps). Avoid mutable state.

Example model passed to the template:

```java
record Invoice(String id, boolean overdue, BigDecimal total, List<Line> lines) {}
record Line(String sku, int qty, BigDecimal price) {}

var model = Map.of("invoice", new Invoice(...));
```

Template use (SpEL):

```text
Overdue: ${invoice.overdue ? 'YES' : 'NO'} — Total: ${invoice.total}
```

## 3) Prefer projections and safe navigation

Use SpEL projections for concise rendering and the `?.` safe navigation operator.

```text
Lines: ${invoice.lines.![sku + ' x' + qty].join(', ')}
Customer: ${order.customer?.name ?: 'N/A'}
```

## 4) Register functionality in Java, not via permissive SpEL

If you need custom behavior, compute it in Java and expose a **pure function result** (or a preformatted string) to the template model rather than enabling `PERMISSIVE` just to call constructors or static methods.

Bad idea (needs permissive features):

```text
${T(java.time.LocalDate).now()}
```

Better: compute on the server and pass a value:

```java
model.put("today", LocalDate.now());
```

```text
Generated on: ${today}
```

## 5) Keep SVG hardened unless all assets are trusted

SVG is XML; in `RESTRICTED` mode the parser mitigates XXE/DTD/entity expansion. Do not relax it unless your assets are controlled.

```java
var cfg = OfficeStamperConfigurations.standard()
    .setSvgSecurityMode(SecurityMode.RESTRICTED); // default
```

## 6) Pitfalls I’ve seen (and how to avoid them)

- Type access via `T()` and constructors: blocked by default for a reason.
- Reaching into application singletons via bean resolvers: resist the urge; precompute instead.
- Leaking domain invariants into expressions: once duplicated in templates, they rot. Keep invariants in code.
- Mixing untrusted user content with permissive SpEL: treat templates as code; apply the principle of least privilege.

## 7) Testing patterns that pay off

- For every non‑trivial template, add a unit or characterization test that stamps with a small model and asserts on key outputs.
- If you toggle `PERMISSIVE` anywhere, add a targeted test that proves why you needed it (and ensure it’s off by default).

## 8) A practical baseline checklist

- [ ] Default to `RESTRICTED` for both SpEL and SVG.
- [ ] Model exposes values, not behavior; prefer DTOs and primitives.
- [ ] Expressions are short and readable; no constructors or type access.
- [ ] Any permissive switch is justified, temporary, and covered by a test.
- [ ] Production runtime enforces `-Dofficestamper.spel.mode=restricted`.

## Worked mini‑example

Context (Java):

```java
var cfg = OfficeStamperConfigurations.standard()
    .setSpelSecurityMode(SecurityMode.RESTRICTED)
    .setSvgSecurityMode(SecurityMode.RESTRICTED);

var model = Map.of(
    "user", Map.of("name", "Ada", "roles", List.of("admin", "author")),
    "stats", Map.of("drafts", 3, "published", 21)
);
```

Template fragments (SpEL):

```text
Hello ${user.name}!
Roles: ${user.roles.join(', ')}
Summary: drafts=${stats.drafts}, published=${stats.published}
```

This uses only data shaping and safe operators; no permissive features required.

---

### May 17th 2026 — Topic Summary
- **Policy**: Default to restricted modes; elevate only for trusted templates.
- **Practice**: Push logic to Java, keep expressions short and safe.
- **Ops**: Lock down with system properties in production.
