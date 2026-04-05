---
layout: article
title: "Office-stamper v3.3 — Vector Graphics and SVG Stamping"
date: 2026-04-05
categories: [ office-stamper ]
tags: [ office-stamper, java, api, release, svg ]
author: Joseph
description: Version 3.3 brings high-fidelity vector graphics support with SVG image stamping, a long-requested feature to move beyond traditional raster formats.
---

Following our [pivot back to Spring SpEL in v3.2]({{ site.baseurl }}{% post_url
2026-03-01-monthly-topic-the-expression-pivot-harnessing-spring-spel %}),
version 3.3 of `office-stamper` addresses a request that has been on my radar
for a long time: high-fidelity vector image stamping.

Until now, image replacement was limited to the default raster formats supported
by Microsoft Word: BMP, PNG, and JPG. While these work for photos, they often
fall short for the kind of technical content I love to generate: diagrams,
charts, and logos.

## Headline Change: SVG Image Stamping

The centerpiece of v3.3 is the support for **Scalable Vector Graphics (SVG)**.
This has been a goal since [Issue #481]({{site.ghos}}/issues/481) was opened
back in September 2024.

### Why SVG?

As someone who frequently integrates **PlantUML, gnuplot, and Mermaid** diagrams
into documentation, I have always preferred SVG. The benefits are two-fold:

1. **Crisper Quality**: Vectors stay sharp regardless of how much you resize
   them in the final Word document or exported PDF.
2. **Smaller Footprint**: For diagrams and simple illustrations, SVGs are often
   significantly smaller than their high-resolution PNG counterparts.

I'm hoping this inspires users to generate their image objects on the fly and
stream them directly into their stamped documents, rather than relying on
pre-rendered assets.

### The "Sharp Edges" of Vector Stamping

Integrating SVGs into the OpenXML (DOCX) format is not as straightforward as
handling raster images. There were several hurdles during implementation:

* **Sizing and Scaling**: SVGs aren't naturally designed with fixed Word
  dimensions in mind. Figuring out the "intended" size from attributes like
  `width`, `height`, `viewBox`, or relative units—and then translating those
  into Word's EMU (English Metric Units)—was a bit of a challenge.
* **Format Differences**: Unlike raster images, SVGs are XML. This means they
  are inserted differently into the document structure. We've used the modern
  `asvg:svgBlip` extension to handle this.
* **Editor Compatibility**: While it works perfectly in modern Microsoft Word,
  it's still an "experimental" frontier for some alternative DOCX editors. If
  you encounter issues in LibreOffice or other viewers, I'd love to hear about
  it so we can refine the implementation.

## Behind the Scenes: Toolchain Refactoring

This release also includes a significant internal cleanup. I've removed
`ByteUtils.java` and `DocxRenderer.java`.

These classes were previously used for a crude "docx-to-string" comparison
during testing to verify image insertions. This legacy approach has now been
fully replaced by my in-house **Docx-to-Asciidoc** library, which provides
reliable verification and diffing capabilities for our test suite.

## Other Changes in v3.3

### New `hasTagAttribute` Utility

A small addition to our `WmlUtils` that allows checking for specific attributes
on smart tags. This is particularly useful for those building complex
conditional logic within their templates.

### Dependency Updates

We've bumped the core toolchain to keep things modern:

- **Spring Core v7.0.6**
- **Jackson v2.21.2**
- **Mockito v5.23.0**
- **Pitest v1.23.0**

## Migration Notes

This is a non-breaking release. If you're already using the `Image` API, you can
now simply pass SVG byte arrays. The engine will automatically detect the format
and handle the SVG-specific logic.

| Feature              | Since | Status  |
|:---------------------|:------|:--------|
| SVG Support          | v3.3  | **New** |
| Spring SpEL Engine   | v3.2  | Stable  |
| Raster Image Support | v1.0  | Stable  |

---

### April 5th 2026 — Release Summary:

- **Feat**: Full support for **SVG image stamping** (Resolves #481).
- **Feat**: New `hasTagAttribute` utility for smart tag inspection.
- **Refactor**: Replaced legacy `DocxRenderer` and `ByteUtils` with internal
  Docx-to-Asciidoc toolchain.
- **Chore**: Updated core dependencies (Spring 7, Jackson 2.21).
