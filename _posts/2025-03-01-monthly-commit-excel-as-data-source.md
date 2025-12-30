---
layout: article
title: "Monthly Commit — Beyond JSON: Using Excel as a Data Source"
date: 2025-03-01
categories: [ office-stamper ]
tags: [ office-stamper, java, excel, business-automation, docs-as-code, agility ]
author: Joseph
description: Bridging the gap between developers and business users by allowing XLSX files to serve as data context for document stamping.
---

In my role as an IT Manager, I’m in constant contact with Finance and HR teams who live almost exclusively in Excel. While I’ve spent years advocating for “docs-as-code” and structured data like JSON or CSV, the reality is that for many stakeholders, the spreadsheet *is* the source of truth.

Instead of spending my limited "downtime" sessions teaching tools that my colleagues will never fully adopt, I decided to meet them where they are. This month’s highlight is the introduction of `ExcelContext`—a pragmatic bridge that lets you "bring your own spreadsheet" as a data source for Office-stamper.

## Why this matters

The goal of Office-stamper has always been to simplify document generation. We previously improved [Contextual Clarity](/office-stamper/2024/11/01/monthly-commit-refactoring-for-contextual-clarity.html) and [Template Normalization](/office-stamper/2024/12/01/monthly-commit-pre-processing-for-template-normalization.html), but we were still largely assuming that the data context would be a Java POJO or a Map prepared by a developer.

By introducing Excel as a native context candidate, we unlock a new workflow:
- **Business Users** maintain the data in a familiar XLSX file.
- **Office-stamper** reads that file and injects it into Word or PowerPoint templates.
- **No POJOs Required**: It builds on our work with the `MapAccessor`, treating the spreadsheet as a dynamic, multi-layered map.

## Implementation: The `ExcelContext`

The core of this change is `ExcelContext`, which implements `Map<String, List<Map<String, String>>>`. It uses `xlsx4j` to traverse a workbook and convert each sheet into a list of records.

### Mapping Strategy

For this initial experimental release, we follow a strict "convention over configuration" approach:
1. **Sheet Name as Key**: Each worksheet in the workbook becomes a key in the top-level map.
2. **First Row as Header**: The first row of each sheet defines the keys for the records in that sheet.
3. **Rows as Records**: Every subsequent row becomes a `Map<String, String>` where keys are the headers.

```java
public class ExcelContext extends AbstractMap<String, List<Map<String, String>>> {
    // ...
    public ExcelContext(SpreadsheetMLPackage spreadsheetPackage) {
        var workbookPart = spreadsheetPackage.getWorkbookPart();
        var workbook = workbookPart.getContents();
        var sheets = workbook.getSheets().getSheet();
        
        var excel = new TreeMap<String, List<Map<String, String>>>();
        for (var sheet : sheets) {
            var worksheetPart = (WorksheetPart) workbookPart.getRelationshipsPart().getPart(sheet.getId());
            var sheetData = worksheetPart.getContents().getSheetData();
            excel.put(sheet.getName(), extractRecords(sheetData.getRow()));
        }
        this.source = Collections.unmodifiableMap(excel);
    }
}
```

## CLI Integration

To make this usable without writing a single line of Java, I’ve updated the CLI using `picocli`. You can now point the stamper to an Excel file as your input:

```bash
# Stamp a PowerPoint deck using data from an Excel sheet
officestamper --input-file data.xlsx --template-file template.pptx --output-path result.pptx --stamper powerpoint
```

## The "Strategic Not Yet" (Experimental Caveats)

Following the [Solo Maintenance](/governance/2025/02/01/solo-maintenance-craftsmanship-in-the-downtime.html) philosophy, this is an "impact slice" rather than a finished product. It comes with some intentional sharp edges:
- **Memory Heavy**: The entire workbook is loaded into memory.
- **Strict Layout**: It expects a clean table starting at A1 with no blank lines.
- **No Formulas**: It extracts the cached string values; it does not re-evaluate Excel formulas.
- **Data Types**: Currently, everything is treated as a String to keep the SpEL resolution simple.

## Impact

This change converts Office-stamper from a developer library into a potential productivity tool for non-technical users. It validates our engine's abstraction: because we decoupled the expression resolution from the data structure, adding Excel support didn't require touching the core stamping logic.

## Next Steps

If this experimental phase is successful, I plan to:
- Resolve specific cells by name or index.
- Support Excel's "Defined Names" and "Tables" for more robust data targeting.
- Implement a streaming reader for large workbooks to avoid memory pressure.

---

### Checklist — Using Excel as Context
- [ ] **Clean Headers**: Ensure your first row contains unique, descriptive names.
- [ ] **Sheet Names**: Use kebab-case or simple names for sheets, as they will be used in your SpEL expressions (e.g., `#{SheetsName[0].HeaderName}`).
- [ ] **No Hidden Rows**: The current extractor reads all rows; filter your data before stamping.
- [ ] **Static Values**: Remember that formulas aren't calculated during extraction—save the workbook to ensure cached values are up to date.
