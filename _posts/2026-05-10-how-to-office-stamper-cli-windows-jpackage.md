---
layout: article
title: "How‑to — Windows Packaging for the Office‑stamper CLI (jpackage: Portable EXE + Installer)"
date: 2026-05-10
categories: [ office-stamper ]
tags: [ office-stamper, cli, windows, packaging, jpackage ]
author: Joseph
description: Package the Office‑stamper CLI into a portable EXE and a Windows installer using jpackage. Includes verification steps and notes on native executable experiments.
---

Long‑term goal: provide a friendly command‑line that can batch‑generate documents from business‑friendly sources — Word/PowerPoint/Excel templates with data from CSV/JSON. This post documents the Windows packaging path with `jpackage`, plus a short note on native executable experiments.

Related issues: [#676]({{site.ghos}}/issues/676), [#677]({{site.ghos}}/issues/677), [#681]({{site.ghos}}/issues/681), [#682]({{site.ghos}}/issues/682).

## Prerequisites

- Windows 10/11 with PowerShell
- JDK 21+ (JDK 25 EA also works) — ensure `jpackage` is available in your JDK
- Optional for MSI: WiX Toolset (v3.14+)

Verify your toolchain:

```powershell
java --version
jpackage --version
```

## 1) Build the CLI JAR

Use Maven from the repository root to produce the CLI artifact (adjust module/profile names if they differ in your fork):

```powershell
cd C:\workspace\github\office-stamper
mvn -DskipTests package
```

Grab the latest CLI JAR (PowerShell picks the most recent):

```powershell
$Jar = (Get-ChildItem -Recurse -Filter *cli*.jar -Path . | Sort-Object LastWriteTime -Desc | Select-Object -First 1).FullName
Write-Host "Using JAR: $Jar"
```

## 2) Create a portable app image (contains an .exe)

This produces a self‑contained directory with a launcher `.exe` — easy to zip and distribute:

```powershell
$AppName = 'office-stamper-cli'
$Version = '3.3.0'  # set from your build/version
$InputDir = Split-Path $Jar
$MainJar  = Split-Path $Jar -Leaf

jpackage `
  --type app-image `
  --name $AppName `
  --app-version $Version `
  --input $InputDir `
  --main-jar $MainJar `
  --main-class pro.verron.officestamper.cli.Main `
  --dest .\dist `
  --icon .\cli\packaging\windows\icon.ico
```

Result:

```
dist/
  office-stamper-cli/
    office-stamper-cli.exe
    runtime/*
    app/*
```

Run it:

```powershell
.& "dist/office-stamper-cli/office-stamper-cli.exe" --help
```

Expected output (excerpt):

```text
office-stamper CLI 3.3.0
Usage: office-stamper [command] [options]
Commands: stamp, version, help
```

## 3) Create a Windows installer (EXE or MSI)

If you prefer an installer (adds Start Menu entries, optional uninstall), use `--type exe` (built‑in) or `--type msi` (requires WiX):

```powershell
jpackage `
  --type exe `
  --name $AppName `
  --app-version $Version `
  --input $InputDir `
  --main-jar $MainJar `
  --main-class pro.verron.officestamper.cli.Main `
  --dest .\dist `
  --win-menu `
  --win-menu-group "Office-stamper" `
  --win-shortcut `
  --win-dir-chooser `
  --icon .\cli\packaging\windows\icon.ico
```

Output example:

```
dist/office-stamper-cli-3.3.0.exe
```

Optional MSI (requires WiX): replace `--type exe` with `--type msi`.

## 4) Verify artifacts

- Version banner:

```powershell
& "dist/office-stamper-cli/office-stamper-cli.exe" --version
```

- SHA256 checksum for distribution:

```powershell
Get-FileHash "dist/office-stamper-cli-3.3.0.exe" -Algorithm SHA256 | Format-List
```

## Troubleshooting

- "jpackage: command not found": ensure you’re using a JDK that bundles `jpackage` (e.g., Oracle/OpenJDK 21+). Re‑open your terminal after install.
- Main class not found: confirm `--main-class pro.verron.officestamper.cli.Main` matches the actual CLI entry point in the repo.
- MSI build fails: install WiX Toolset and ensure `candle.exe`/`light.exe` are on PATH.
- SmartScreen warnings: consider code‑signing the installer (`signtool sign /fd SHA256 /a <file.exe>`); keep a timestamp server for longevity.

## Native executable experiments (early)

I’m also experimenting with producing a native binary (no bundled JVM). This currently requires GraalVM `native-image` and careful reflection config for libraries like Spring SpEL and OpenXML. It’s not ready for general use yet (see [#676]({{site.ghos}}/issues/676), [#677]({{site.ghos}}/issues/677))).

Example invocation (will need additional configuration files):

```powershell
# Requires GraalVM and reflection/resource configs
native-image `
  --no-fallback `
  -H:Name=office-stamper `
  -cp $Jar `
  pro.verron.officestamper.cli.Main
```

## Why this direction

Packaging the CLI lowers the barrier for non‑Java users and sets the stage for batch generation from analyst‑friendly formats (DOCX/PPTX/XLSX templates + CSV/JSON data) without IDE setup. This aligns with the long‑term vision called out in [#681]({{site.ghos}}/issues/681) and [#682]({{site.ghos}}/issues/682).

---

### May 10th 2026 — Guide Summary
- Package a portable app image with `jpackage` and run `office-stamper-cli.exe` directly.
- Produce a Windows installer (`--type exe` or `--type msi`) and verify with SHA256.
- Native image is experimental; tracked in issues and not recommended for production yet.
