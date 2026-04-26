---
layout: article
title: "How‑to — ExcelContext: Expose Excel Structure to SpEL (Sheets, Cells, Tables)"
date: 2026-05-24
categories: [ office-stamper ]
tags: [ office-stamper, excel, spel ]
author: Joseph
description: Use ExcelContext to navigate worksheets, A1 cells, and named tables from SpEL without custom resolvers.
---

This guide shows how to expose an `.xlsx` workbook to your templates through `ExcelContext`, so SpEL can query **sheets**, individual **cells** by A1 address, each sheet’s default **table** (header row + records), and **named tables** by name.

Goal (May 2026): focus on structure and ergonomics, not performance. See related issue: [#678]({{site.ghos}}/issues/678) and early tracking [#81]({{site.ghos}}/issues/81).

## Create an ExcelContext

```java
import pro.verron.officestamper.excel.ExcelContext;

// From a Path
var excel = ExcelContext.from(Path.of("data/report.xlsx"));

// Or from an InputStream (consumed during construction)
try (var is = Files.newInputStream(Path.of("data/report.xlsx"))) {
    var excel2 = ExcelContext.from(is);
}
```

Pass it as part of your model when stamping:

```java
var cfg = OfficeStamperConfigurations.standard();
var stamper = OfficeStampers.docxStamper(cfg);

var model = new HashMap<String, Object>();
model.put("excel", excel);

stamper.stamp(
    Files.newInputStream(Path.of("templates/summary.docx")),
    model,
    Files.newOutputStream(Path.of("build/summary-out.docx"))
);
```

## What SpEL can access

The context behaves like a nested map. Keys and conventions:

- `excel["sheets"]` → list of sheets (each a `SheetContext`).
- `excel["Sheet1"]` → the default table (header row + records) of the sheet named `Sheet1`.
- `excel["SomeNamedTable"]` → a named table materialized as a list of records.
- For a specific sheet context, use:
  - `sheet["A1"]` → value of cell A1
  - `sheet["rows"]` → the default table (first row = headers) as a list of maps

### Examples in a template

```text
Total (B2 on first sheet): ${excel.sheets[0]['B2']}

First three rows from the default table on 'Sheet1':
${#lists.take(excel['Sheet1'], 3).![toString()].join('\n')}

Iterate a named table 'Orders' and show two columns:
${#each excel['Orders']}
• ${it['Order ID']}: ${it['Amount']}
${/each}
```

Note: for header keys containing spaces, index with `['Header Name']`.

## Cell addressing and header mapping

- Cells are addressed via standard A1 notation (e.g., `C7`).
- The default table for a sheet uses the first row as headers; each subsequent row becomes a record mapping `header → value`.
- Named tables (Excel “structured tables”) are exposed by their table name and rendered to the same list‑of‑records structure.

## Troubleshooting

- A cell or table returns `null`:
  - Verify the sheet/table name spelling (case‑sensitive).
  - Ensure the cell address exists in the sheet (A1 notation).

- Headers look empty:
  - Make sure the first row of the range contains headers. Blank cells will map to empty string keys.

## Where this is going

- Short‑term: enrich addressing helpers (e.g., `row(n)`, `col(n)`) and improve type coercion beyond formatted strings.
- Medium‑term: combine with the CLI to batch‑generate documents from Excel/CSV/JSON with zero custom resolvers.

---

### May 24th 2026 — Guide Summary
- **Feat**: Query worksheets (`sheets`), A1 cells, default sheet tables (`rows`), and named tables via SpEL.
- **Usage**: Add `ExcelContext` to your model; access via simple key lookups.
- **Focus**: Structure and ergonomics; perf work comes later.
