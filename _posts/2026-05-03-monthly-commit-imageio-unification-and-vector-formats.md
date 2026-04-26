---
layout: article
title: "Monthly Commit — Unifying the Image Pipeline (SVG/EMF/WMF via ImageIO)"
date: 2026-05-03
categories: [ office-stamper ]
tags: [ office-stamper, java, imageio, svg, emf, wmf ]
author: Joseph
description: Consolidating vector and raster image handling with custom ImageIO plugins, introducing SVG as a first-class citizen while keeping EMF/WMF compatibility.
---

In April, I shipped v3.3 with **SVG stamping** as the headline feature. This month’s commit goes deeper: I unified how `office‑stamper` handles images—raster and vector—by moving to a modern **ImageIO‑based pipeline** with custom plugins.

## Context and motivation

Historically, `docx4j` provided decent support for raster formats and even legacy vectors **WMF/EMF**, but crucially lacked **SVG**. Java’s built‑in `ImageIO` supports common rasters (PNG/JPG/BMP) but no vectors at all (no SVG/EMF/WMF). Third‑party readers I tried typically covered a single vector format, never all three.

Long‑term, I want to reduce `docx4j` dependencies. So I rolled my own **ImageIO plugins** and consolidated detection/handling in one place. SVG is the hero, WMF/EMF are there for compatibility.

Related issues: [#583]({{site.ghos}}/issues/583), [#676]({{site.ghos}}/issues/676), [#677]({{site.ghos}}/issues/677).

## Headline changes

- New ImageIO readers for **SVG**, **EMF**, and **WMF** (behind a clean abstraction).
- Centralized part creation and format detection (internal `ImgPart`/utils).
- Reworked `Image` preset to accept `InputStream` directly and cache bytes lazily.
- Reuse existing images already embedded in the DOCX when possible (dedupe).

These changes align with the April release but complete the story by bringing EMF/WMF into the same path as SVG.

## Minimal example — stamping from streams

```java
import pro.verron.officestamper.preset.Image;
import pro.verron.officestamper.api.OfficeStamperConfiguration;
import pro.verron.officestamper.preset.OfficeStamperConfigurations;
import pro.verron.officestamper.preset.OfficeStampers;

try (var in = getClass().getResourceAsStream("/assets/logo.svg")) {
    var cfg = OfficeStamperConfigurations.standard();
    var stamper = OfficeStampers.docxStamper(cfg);

    var image = new Image(in, /* maxWidthTwip */ null, "logo.svg", "Company Logo");

    stamper.stamp(
        getClass().getResourceAsStream("/templates/brand.docx"),
        Map.of("companyLogo", image),
        Files.newOutputStream(Path.of("build/brand-out.docx"))
    );
}
```

Notes:
- The `Image` preset now takes an `InputStream` (no mandatory pre‑buffering). Internally it caches bytes once.
- Format detection is automatic; SVG will go through the vector path, PNG/JPG via raster.

## Sizing SVGs correctly (viewBox → EMU)

SVG size is derived from `width`/`height` or `viewBox` when present. We convert to Word’s **EMU** (English Metric Units). If your SVG has only a `viewBox`, you still get a sensible default; you can constrain with `maxWidth` in twips when constructing `Image`.

```java
var image = new Image(svgStream, /* maxWidthTwip */ 6000, "diagram.svg", "Sequence diagram");
```

## Reusing images already in the DOCX

When stamping multiple times with the same image, the engine will reuse the existing `imageX` part if content matches, minimizing package bloat. This is automatic; no extra API.

## Compatibility notes

- Microsoft Word: fully supported for SVG/EMF/WMF.
- Other editors (LibreOffice, Apple Pages): SVG support varies; WMF/EMF may render differently. Please report concrete cases with sample files.

## Impact

- Sharper diagrams and logos with SVG; smaller files compared to high‑DPI PNG.
- Unified pipeline simplifies maintenance and paves the way to reduce `docx4j` coupling.

## Next steps

- Fine‑tune DPI heuristics for edge cases.
- Continue reducing `docx4j` runtime surface.
- Explore vector fallbacks for non‑SVG viewers.

---

### May 3rd 2026 — Commit Summary
- **Feat**: Custom ImageIO plugins for **SVG/EMF/WMF**; unified format detection.
- **Feat**: `Image` now accepts `InputStream`; synchronized byte caching.
- **Feat**: Reuse identical images already present in the DOCX package.
- **Refactor**: Centralized part creation for images across the engine.
