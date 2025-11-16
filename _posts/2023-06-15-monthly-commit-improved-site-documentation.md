---
layout: article
title: "Making Knowledge a Feature: Improved Site Documentation"
author: joseph-verron
date: 2023-06-15 09:00:00
categories: [office-stamper]
tags: [docs-as-code, office-stamper, documentation, ci, asciidoctor, agility, craftsmanship, solo-maintainer, enterprise, developer-experience]
description: "Turning the website into a first-class, build-verified artifact: clearer concepts, copy‑paste examples, and CI‑friendly docs that scale a solo‑maintained library inside enterprises."
---

TL;DR: Docs are product. This change turns the site into a first‑class, build‑verified artifact: clearer concepts, copy‑paste examples that compile, and CI hooks so docs don’t drift.

## Quick Facts

| Aspect    | Details                                                               |
|-----------|-----------------------------------------------------------------------|
| Commit    | [8243bd7](https://github.com/verronpro/office-stamper/commit/8243bd7) |
| Scope     | Readme/site format, structure, examples, navigation                   |
| Why       | Reduce support, speed onboarding, align site with APIs                |
| Outcome   | Fewer “how do I…?” questions; faster, safer adoption                  |

## Why this is May’s highlight

For a project maintained by one person and adopted inside big companies, documentation is a force multiplier. These changes make Office‑stamper easier to learn, evaluate, and extend. Short‑term: fewer ad‑hoc questions. Long‑term: stability—clear intent lets code evolve without leaving users behind.

Docs‑as‑code is the operating model. The same repository ships the engine, and the site; docs are reviewed, versioned, and built alongside the code. When APIs change, the docs change in the same PR. When examples age, CI catches the drift. Teams can deep‑link to a page and commit; auditors can trace how guidance evolved.

## What actually changed

- Reworked core concepts (placeholders, comment processors, resolvers) with task‑oriented headings.
- Added small, copy‑paste examples that compile against public types—no pseudo‑code.
- Tightened terminology so names in Javadoc and the site align (one noun, one meaning).
- Improved structure/navigation to reduce pogo‑sticking between pages.
- Fixed rendering glitches and link targets to make deep‑linking reliable.

Together, these form a coherent experience: the site now guides readers from “What can this do?” → “Stamp my first document” → “Extend it safely,” without boiling the ocean.

## Before → After (mini example)

Goal: move from hand‑wavy snippets to compile‑ready examples and CI‑friendly docs.

Before (README.md fragment):

```java
DocxStamper stamper = new DocxStamperConfiguration().build();
stamper.stamp(template, context, out);
```

After (complete, imports + context + close):

```java
import java.io.InputStream;
import java.io.OutputStream;
import java.util.Map;
import pro.verron.officestamper.DocxStamper;
import pro.verron.officestamper.DocxStamperConfiguration;

class Example {
  public static void main(String[] args) throws Exception {
    Map<String, Object> context = Map.of("name", "World");
    try (InputStream template = Example.class.getResourceAsStream("/hello.docx");
         OutputStream out = java.nio.file.Files.newOutputStream(java.nio.file.Path.of("hello-out.docx"))) {
      DocxStamper stamper = new DocxStamperConfiguration().build();
      stamper.stamp(template, context, out);
    }
  }
}
```

Format: move longform site content to AsciiDoc (for richer includes), keep Markdown for the blog; wire Asciidoctor in CI so broken anchors fail fast.

## Agile and craftsmanship lens

- Short feedback loops: runnable examples let readers self‑serve.
- Single source of truth: contracts live in Javadoc; guides link to them. No duplication, no drift.
- Small, steady improvements: ship narrow changes that improve one path at a time.

## Solo‑maintainer + enterprise usage angle

As a solo maintainer, I cannot be the help desk. The best way to scale is to turn recurring answers into durable artifacts. For enterprise teams picking up Office‑stamper on a short engagement, these upgrades provide self‑contained pages with prerequisites, realistic examples, and direct API links. Procurement and security reviewers benefit from co‑located design intent and extension boundaries.

## CI/Automation: verify docs during build

Add an Asciidoctor step to the Maven site build so missing includes/anchors break CI. Example:

```xml
<!-- pom.xml (site profile excerpt) -->
<plugin>
  <groupId>org.asciidoctor</groupId>
  <artifactId>asciidoctor-maven-plugin</artifactId>
  <version>2.2.3</version>
  <executions>
    <execution>
      <goals>
        <goal>process-asciidoc</goal>
      </goals>
      <configuration>
        <sourceDirectory>${project.basedir}/src/site/asciidoc</sourceDirectory>
        <attributes>
          <toc>left</toc>
          <sectanchors>true</sectanchors>
          <sectnums>true</sectnums>
        </attributes>
      </configuration>
    </execution>
  </executions>
  <dependencies>
    <!-- keep plugin version pinned for reproducibility -->
    <dependency>
      <groupId>org.asciidoctor</groupId>
      <artifactId>asciidoctorj</artifactId>
      <version>2.5.11</version>
    </dependency>
  </dependencies>
  </plugin>
```

Optional link check (run occasionally for heavy pages): use `htmlproofer` or a lightweight script to validate external links.

## Risks and mitigations

- Duplicated explanations between the site and code comments → push contracts/edge cases to Javadoc; keep site focused on how/why and link to API.
- Examples drifting as APIs evolve → prefer examples that compile; verify includes/anchors in CI.
- Too much content → prune aggressively; bias toward task‑oriented flows over encyclopedic coverage.

## How to apply this in your project (runbook)

1) Co‑locate docs with code. Update docs in the same PR as API changes.
2) Convert narrative guides to task‑oriented pages; add one runnable example per page.
3) Add an Asciidoctor (or Markdown) build step to CI; fail on broken includes.
4) Link site pages to Javadoc for contracts; link Javadoc back to guides.
5) Add a docs checklist to Definition of Done for user‑visible changes.

## Production checklist

- [ ] Each guide has a runnable, copy‑paste example
- [ ] Javadoc contains contracts; guides link to it
- [ ] CI builds the site/docs and fails on broken includes
- [ ] Deep‑links (anchors) are stable and tested
- [ ] Terminology is unified across site and Javadoc
- [ ] Pages are short, scannable, and task‑oriented

## References

- Commit: [8243bd7](https://github.com/verronpro/office-stamper/commit/8243bd7)
- Related: site layout and concept pages under `engine/src/site/asciidoc/`
- Javadoc packages: `pro.verron.officestamper.api`, `core`
