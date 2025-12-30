---
layout: article
title: "Unit Tests Don’t Mean What You Think: Characterization Testing WordprocessingML in Office‑stamper"
date: 2024-03-01
categories: [ office-stamper ]
tags: [ testing, docs-as-code, office-stamper, wordprocessingml, java ]
author: Joseph
description: "I replaced brittle XML‑level assertions with a textual, AsciiDoc‑like view of DOCX output. Here’s how characterization tests made Office‑stamper’s refactors safer and test suites readable."
---

When you stamp DOCX, “unit tests” that assert on internal XML shape are a trap. WordprocessingML is a forest of paragraphs, runs, SDTs (content controls), fields, bookmarks, headers/footers, and paste‑artifacts. Tiny refactors cause harmless run splits/merges or node reordering, and brittle tests explode. Users don’t care about the exact run tree; they care that the result reads right, looks right, and keeps invariants intact.

For Office‑stamper (a Java library that fills placeholders and executes comment‑based Spring SpEL instructions to repeat or conditionally render content), I switched to characterization tests over a textual, AsciiDoc‑like representation of the final document. I call the helper the `Stringifier`. Instead of spelunking XML, tests compare strings that reveal text, styles, table and note structure—what users actually perceive.

## What changed — from internal shape to observable behavior

Characterization tests, for this domain, mean: build tiny fixtures that exercise one tricky pattern, stamp the document, stringify the full output, and assert on that string. It’s still a unit test (small fixture, fast, deterministic), but the “unit” is observable behavior, not DOM topology. Readability jumped; refactors became safe.

## The invariants we protect

- Comments with executed instructions are removed after processing.
- Placeholders may span runs but never cross paragraphs; the resulting style is taken from the first run.
- SDTs can contain placeholders, but placeholders must not cross SDT boundaries.
- Orphaned fields, bookmarks, footnotes, and endnotes are cleaned up.
- Run splitting is acceptable only at placeholder boundaries (start/end), never to change semantics.
- Resulting styles come from the document’s existing styles; defaults are applied by instruction processors and resolvers.
- Tables: placeholders never span cells; comment processors may affect a whole row/table even if authored inside a smaller element—so users control execution order without violating cell boundaries.
- Headers, footers, footnotes, and endnotes are processed consistently.

## Worked examples (stringified)

All tests are JUnit 5. I now assert on the `Stringifier` output rather than the raw XML.

1) Placeholder across runs

Input pattern: placeholder split over three runs; bold begins, underline ends.

```text
❬It should be bold: ${pla❘{b=true}❭❬cehol❘{b=true}❭❬der} and then underlined.❘{b=true}❭
```

Expected (placeholder remains atomic; first run style applies to the token):

```text
❬It should be bold: ❘{b=true}❭❬Homer Simpson❘{b=true}❭❬ and then underlined.❘{b=true}❭
```

Assertion sketch (pseudo‑code):

```text
assertEquals(
  "❬It should be bold: ❘{b=true}❭❬Homer Simpson❘{b=true}❭❬ and then underlined.❘{b=true}❭",
  stringifier.stringify(stamp(input))
)
```

2) Repeat table rows

Input pattern: a row‑scoped comment processor repeats the row for each entry; first column is character, second is voice actor. Cell config styles (`cnfStyle`) are preserved.

```text
|===
| Character name
| Voice actor<cnfstyle=100000000000>

| <1|>${character}
| ${actor}<cnfstyle=000000100000><|1><1|repeatTableRow(names)>

|===
```

Expected:

```text
|===
|Character name
|Voice Actor<cnfStyle=100000000000>

|Homer Simpson
|Dan Castellaneta<cnfStyle=000000100000>

|Marge Simpson
|Julie Kavner<cnfStyle=000000100000>

|Bart Simpson
|Nancy Cartwright<cnfStyle=000000100000>

|Kent Brockman
|Harry Shearer<cnfStyle=000000100000>

|Disco Stu
|Hank Azaria<cnfStyle=000000100000>

|Krusty the Clown
|Dan Castellaneta<cnfStyle=000000100000>

|===
```

3) Conditional display with notes

Input pattern: a `displayTableRowIf(false)` on Homer’s row in a table of Simpsons family members; each row carries a detail and an endnote. Removing the row should also remove related notes and keep numbering coherent.

Expected (Homer removed; remaining rows/notes coherent):

```text
|===
|Character
|Role<cnfStyle=100000000000>
|Fun Fact<cnfStyle=100000000000>

|Marge Simpson
|Matriarch<cnfStyle=000000000000>
|Her hair once hid an entire toolbox❬[2]❘{rStyle=Appeldenotedefin}❭.<cnfStyle=000000000000>

|Bart Simpson
|Eldest Child<cnfStyle=000000100000>
|Bart's famous catchphrase is "Eat my shorts!"❬[3]❘{rStyle=Appeldenotedefin}❭.<cnfStyle=000000100000>

|Lisa Simpson
|Middle Child<cnfStyle=000000000000>
|Lisa is a talented saxophonist❬[4]❘{rStyle=Appeldenotedefin}❭.<cnfStyle=000000000000>

|Maggie Simpson
|Youngest Child<cnfStyle=000000100000>
|Maggie is known for her pacifier and silent wisdom❬[5]❘{rStyle=Appeldenotedefin}❭.<cnfStyle=000000100000>

|===
[endnotes]
---
[2] Marge's hairdo was designed to hide various items, a nod to cartoon logic.
[3] Bart's rebellious attitude is encapsulated in this catchphrase.
[4] Lisa's musical talent often shines through her saxophone solos.
[5] Despite her silence, Maggie has saved her family on multiple occasions.
---
```

## Why this works

- Readability: engineers and maintainers can glance at a string diff and understand the behavioral change.
- Stability: harmless XML churn (node order, run technicalities) won’t break tests.
- Safety: when a real invariant changes, diffs are loud and specific.

This fits my reality as a solo maintainer with sporadic enterprise usage: I need confidence to refactor and a fast path from bug report → minimal fixture → fix → guard.

## Tooling and CI

- Tests: JUnit 5, plain `assertEquals` on `Stringifier` output; no deep DOM assertions.
- The `Stringifier` evolves: when I discover a new element that should appear in the textual view, I update the renderer and refresh golden outputs where the invariant applies. This keeps the suite honest without micromanaging XML.
- CI: GitHub Actions runs Maven build, tests, site, Javadoc, mutation tests, deploys to Maven Central, and hooks into SonarQube/CodeQL. Renovate keeps dependencies fresh.

## Pitfalls and mitigations

- Golden file churn: keep fixtures tiny and named for the behavior they capture; batch renderer changes with a clear changelog.
- Over‑stringifying: don’t aim to mirror WordprocessingML 1:1; capture only user‑visible semantics (text, styles, table/notes structure).
- Blind spots: add focused fixtures whenever a user report reveals a missed invariant (headers/footers, SDTs, bookmarks).

## Checklist — getting started with characterization tests

1. List 3–5 tricky patterns from real templates (split placeholders, SDTs, repeated rows, notes).
2. Build minimal fixtures for each pattern; keep them human‑readable.
3. Introduce a `Stringifier` that exposes only user‑visible semantics.
4. Assert invariants with plain `assertEquals`; avoid DOM traversal.
5. Let CI enforce the new tests; evolve the renderer deliberately.

Unit tests still matter—but the “unit” is the behavior your users can see, not the graph your library happens to build this week.
