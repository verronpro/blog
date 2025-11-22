---
layout: article
title: "How I Stopped Trying to Control Everything and Learned to Love Boring CI"
date: 202X-XX-XX 09:00:00
categories: [ TODO ]
tags: [ ci-cd, agility, craftsmanship, docs-as-code,
        solo-maintainer, enterprise, windows, ubuntu ]
author: Joseph
description: "Why I embraced boring, constraint-friendly CI after realizing I'm not Google and don't need fancy pipelines."
---

# The Fancy Pipeline Trap

When I first started with office-stamper, I was enamored with sophisticated CI/CD pipelines. I wanted everything: custom
runners, complex deployment strategies, fancy monitoring, and bleeding-edge tools. After all, that's what I was used to
in corporate environments.

What a waste of time.

# The Solo Maintainer Reality Check

As a solo maintainer, I'm not Google. I don't have a platform team, dedicated DevOps engineers, or 24/7 monitoring. More
importantly, office-stamper is used by teams in large organizations with restrictive environments:

- Outbound network locked down
- Custom certificate stores
- No desktop apps installed
- Strict JDK baselines

If my CI required exotic setups, it would be irrelevant to my users' reality.

# My Asciidoctor-Inspired Approach to CI Simplicity

Just like how I learned to appreciate Asciidoctor's balance of power and simplicity, I realized that CI needed to be
boring to be effective. Here's what I mean by "boring":

## Use Mainstream Tools

I stick to widely supported tools like Maven Surefire, Failsafe, and Asciidoctor plugins. No experimental runners or
cutting-edge technologies that might break in six months.

## Explicit Over Implicit

Everything is explicitly configured. JDK versions, toolchains, dependencies - nothing is left to environment
assumptions. This makes the pipeline readable and reproducible.

## Cross-Platform from Day One

I test on both Windows and Ubuntu because my users run both. This catches path, encoding, and filesystem differences
early - before my users encounter them.

# Documentation as Part of the Build

Here's where my love for documentation tools really shines. I treat documentation as part of the build process, not an
afterthought:

- Build the AsciiDoc site in CI
- Fail the build on broken links or missing documentation
- Validate that documentation examples actually work

This approach comes from my experience with tools like Doxygen - documentation that's validated and maintained alongside
code.

# The Constraint-Driven Development Revelation

Working with constrained environments taught me an important lesson: constraints drive better design. When I can't rely
on fancy tools or complex setups, I'm forced to write simpler, more robust code.

This is similar to how Markdown wins over more complex markup languages - not because it's more powerful, but because
it's simpler and more widely supported.

# My CI Philosophy: Predictable Over Powerful

After trying to be clever with CI, I've embraced being predictable:

## Keep the Build Boring

Use mainstream plugins and avoid exotic runners. Prefer LTS JDKs and explicit toolchain configuration. When someone
looks at my CI configuration, they should understand it immediately.

## Test Like a User

I exercise the CLI with small samples (CSV, JSON, XML/HTML, Properties) to verify end-to-end functionality. These
samples are committed to the repo to avoid network dependencies.

## Helpful Logs by Default

When a test fails in CI, the logs explain what happened in domain terms. This comes from my experience with test engine
reports - good error messages are as important as good code.

# The Solo Maintainer's CI Toolkit

As a solo maintainer, my CI configuration serves multiple purposes:

1. **Runbook**: It documents how the project is built and tested
2. **Support Playbook**: When users report issues, I can point to a working example
3. **Quality Gate**: It prevents regressions and maintains standards
4. **Communication Tool**: It shows enterprise users that I understand their constraints

# Practical Implementation for Solo Maintainers

If you're maintaining a project alone:

1. **Start simple**: A basic matrix build on standard platforms
2. **Add documentation**: Make docs part of your build process
3. **Embrace constraints**: They'll make your code better
4. **Automate quality checks**: Use tools like CodeQL and SonarQube
5. **Keep it readable**: Future you (and potential contributors) will thank you

# The Unexpected Benefit

Boring CI made me a better developer. By focusing on simplicity and reliability rather than cleverness, I created a
system that works for both me and my users. My CI pipeline became documentation, quality assurance, and a communication
tool all in one.

Sometimes the most sophisticated solution is the simplest one.
