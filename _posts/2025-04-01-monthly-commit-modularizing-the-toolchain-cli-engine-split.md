---
layout: article
title: "Modularizing the Toolchain: The CLI/Engine Split"
date: 2025-03-01
categories: [ office-stamper ]
tags: [ office-stamper, java, cli, modularization, craftsmanship, jpackage, agility ]
author: Joseph
description: Decoupling the CLI from the core engine to enforce API boundaries, enable native distribution, and reduce maintenance noise.
---

For a long time, the `examples` module in Office-stamper served two masters: it was both a playground for users to see how to use the library and the source of our command-line interface. As the project grew, this "cramped" arrangement started to leak. Engine-specific concerns were mixing with CLI-specific dependencies like `picocli`, and it was becoming too easy to accidentally depend on internal core classes that were never meant to be exported.

This month, I finally made the split. By promoting the CLI to its own dedicated module, we've created a clean boundary that protects the engine's purity while providing a robust, self-sufficient tool for our users.

## Why now: Enforcing the Boundary

The primary driver for this change was encapsulation. I wanted a dedicated space where I could tell people to look at how an Office-stamper client is implemented without the risk of them (or me) importing non-exported core classes.

By moving to a separate `office-stamper-cli` module, we've turned the library's consumer experience into a "canary." If I change an interface in the engine, and the CLI breaks, I know immediately that I've violated a contract. This provides a much tighter feedback loop than the old `examples` package did.

## What changed

- **Module Promotion**: Deleted the `examples` module and created a first-class `cli` module.
- **Native Distribution**: Integrated the `jpackage-maven-plugin` to produce native executables (EXEs).
- **Toolchain Hygiene**: Cleaned up the `pom.xml` structure, replacing hardcoded strings with `${project.*}` variables and removing redundant plugin configurations.
- **Built-in Diagnostics**: Migrated the environment-capturing `Diagnostic` tool into the CLI as a standard feature.

## Implementation: Native Distribution with jpackage

One of the most exciting additions is the use of `jpackage`. I want the tool to be self-sufficient; a user shouldn't have to worry about their Java installation just to run a diagnostic report or stamp a document.

```xml
<plugin>
    <groupId>com.github.akman</groupId>
    <artifactId>jpackage-maven-plugin</artifactId>
    <version>0.1.5</version>
    <configuration>
        <name>${project.artifactId}</name>
        <appversion>${project.version}</appversion>
        <copyright>${copyright}</copyright>
        <vendor>${vendor.name}</vendor>
        <mainjar>${project.artifactId}-${project.version}-jar-with-dependencies.jar</mainjar>
        <mainclass>pro.verron.officestamper.Main</mainclass>
        <type>EXE</type>
        <input>target/</input>
    </configuration>
</plugin>
```

This ensures that the "try-out" experience is as frictionless as possible: download an EXE, point it at a template, and go.

## The "Boring" Hygiene: Asynchronous Communication

During this refactor, I spent significant time on "boring" POM cleanup. To some, this might look like bike-shedding, but for a [Solo Maintainer](/governance/2025/02/01/solo-maintenance-craftsmanship-in-the-downtime.html), it is essential.

I view build warnings as a form of **ultimate asynchronous communication**. A warning in the IDE or the build log is a prompt from the toolchain authors letting me know there's a topic I'm not yet aware of—perhaps a future change I should anticipate, or a better pattern I should adopt. By keeping the build "clean-as-you-go," I reduce the noise and allow these "lessons" to stand out when they appear.

## Impact

- **Zero-Install Trial**: Users can now run Office-stamper as a native tool, lowering the barrier to entry for non-Java developers.
- **Reliable Diagnostics**: The bundled `Diagnostic` template and data collector act as a reliable "smoke test" for the entire pipeline.
- **Clearer Documentation**: The CLI code now serves as the canonical example of how to implement a client, using only the public API of the engine.

## Next Steps

Now that the CLI is its own entity, I plan to:
1. Expand the `jpackage` configuration to support macOS and Linux installers.
2. Add more built-in "keyword" templates beyond the `diagnostic` one to showcase complex features like [Excel data sources](/office-stamper/2025/03/01/monthly-commit-excel-as-data-source.html).
3. Implement a plugin architecture for the CLI to allow users to add their own custom resolvers without re-compiling the core.

---

### Checklist: CLI Best Practices
- [x] **Separate Concerns**: Keep the CLI skin (parsing, I/O) separate from the engine logic.
- [x] **Native First**: Provide self-contained binaries to avoid "Java-dependency" friction.
- [x] **Clean Build**: Treat warnings as a learning opportunity; don't let them accumulate.
- [x] **Canary Testing**: Use your own CLI as the primary integration test for your library's API.
