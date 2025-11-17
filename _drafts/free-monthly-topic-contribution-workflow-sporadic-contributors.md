---
layout: article
title: "How I Learned to Love Sporadic Contributors (Even Though I Miss Mob Programming)"
date: 202X-XX-XX 09:00:00
categories: [ TODO ]
tags: [ agility, craftsmanship, docs-as-code, solo-maintainer, enterprise,
        contribution, governance ]
author: Joseph
description: "Why I had to adapt my teamwork-loving approach to accommodate the reality of sporadic open source contributions, and how I make it work."
---

# Missing Mob Programming in Solo Land

I love mob programming. There's something magical about having a whole team tackle a problem together - the real-time
feedback, the collective problem solving, the instant code review. In my corporate days, this high-quality gatekeeping
enhanced our long-term velocity, even when it seemed slow at first.

But now I'm a solo maintainer. No more mobs, no more instant feedback, no more team discussions. Just me, my code, and
the occasional contributor who appears every few months with a problem to solve.

# The Sporadic Contributor Challenge

Office-stamper is used by teams inside big companies who integrate it for a few weeks, then disappear for months. When
they reappear, it's with different constraints and new problems. These contributors don't have time to learn the entire
codebase - they need to make their change and move on.

Initially, I treated these contributions like I would in a team setting. I expected detailed discussions, multiple
iterations, and deep understanding of the codebase. What a waste of everyone's time!

# My Adapted Workflow: Teamwork Principles, Solo Implementation

I realized I needed to preserve the quality mindset I loved from mob programming while adapting to the reality of
sporadic contributions. Here's what I do now:

## Start with the Problem, Not the Solution

Before any code, contributors open an issue describing the user problem. I don't need them to understand the entire
codebase - just explain what they're trying to accomplish. This mirrors the problem-framing part of mob programming
where we'd spend time understanding the issue before diving into solutions.

## Tiny Reproductions Are Gold

Contributors provide a minimal reproduction - either a tiny .docx file or a code snippet. This is like the "example
test" we'd write first in mob programming. It captures the essence of the problem without the noise.

## Small, Focused Changes

I insist on small, focused PRs. This isn't just about reviewability - it's about maintaining the quality feedback loop I
miss from mob programming. Small changes are easier to review thoroughly, and I can provide more targeted feedback.

## Documentation as Collaboration

Every behavior change must include updated documentation. This serves multiple purposes:

- It forces contributors to think about how others will use their feature
- It provides a communication channel between us
- It helps future users (and future me) understand the intent

# The Automation Layer: My Virtual Teammates

Since I can't mob program, I've built a virtual team using automation:

- **CI/CD** provides immediate feedback on build and test failures
- **CodeQL and SonarQube** catch common issues automatically
- **Renovate** keeps dependencies current without manual effort
- **Maven checks** enforce documentation requirements

These tools provide the kind of immediate, consistent feedback that teammates would give in a mob programming session.

# What I Tell Enterprise Contributors

Enterprise contributors work under constraints - timeboxed sprints, restricted environments, corporate firewalls. My
workflow honors that reality:

- Keep all context in the issue/PR, not in private messages
- Expect asynchronous review - many enterprise networks block GitHub notifications
- Link to relevant documentation and previous discussions
- Focus on user-facing behavior, not implementation details

# The Reality Check

This isn't ideal. I miss the energy of a good mob programming session, the satisfaction of solving a problem together,
the learning that comes from seeing how others approach challenges.

But this system works. It keeps office-stamper maintainable, makes contributors feel heard, and preserves the quality
standards I believe in - even when I'm flying solo.

# For Contributors: How to Get Your PR Merged Faster

1. **Explain the problem clearly** - Don't assume I know your use case
2. **Include a minimal reproduction** - Help me understand and test your issue
3. **Keep changes small and focused** - Large changes are hard to review thoroughly
4. **Update the documentation** - Help future users understand your feature
5. **Be patient with asynchronous review** - I'm not ignoring you, I'm just one person

The goal is to make contributions feel like cooperation, not negotiation - even when we're not in the same room (or even
the same time zone).

# Why this topic for September

Office‑stamper is maintained by one person and adopted sporadically
inside big companies. That means outside contributions tend to arrive
once every two or three months—often from engineers who don’t have time
to learn the entire codebase. September is a good moment to document the
contribution workflow that keeps the project welcoming and the
maintenance load sane. These practices are not fancy; they are optimized
for clarity, small batches, and asynchronous collaboration across time
zones and corporate firewalls.

# The problem we’re really solving

“Sporadic contribution” is a different beast from “open‑source at
scale.” Contributors appear, do valuable work for a short window, then
disappear. Without a simple workflow, PRs stall, reviewers forget
context, and the next adopter finds a confusing trail. Meanwhile, as a
solo maintainer, I need an approach that preserves energy for core
engineering rather than process thrash.

# A lightweight, repeatable workflow

- Start with an issue that frames intent Before code, open an issue
  describing the user problem and the proposed direction. Keep it short.
  Include links to the relevant docs pages or Javadoc packages (e.g.,
  `pro.verron.officestamper.api`). If you’re proposing a new processor
  or resolver, sketch the DSL and a tiny example.

- Provide a minimal reproduction or fixture If your change fixes a bug,
  attach a tiny `.docx` that demonstrates the problem. If that’s not
  possible, paste a code snippet that builds a similar tree with
  `WmlFactory` helpers. Repro artifacts are gold: they make reviews and
  tests crisp.

- Slice changes into reviewable PRs Prefer two small PRs over one
  mega‑PR: one for refactors/renames, one for behavior changes. Keep
  each PR focused on a single intent and include a short “why” in the
  description.

- Update docs in the same PR If a change affects user‑visible behavior
  or adds a capability, update the relevant AsciiDoc page and/or
  Javadoc. Treat docs as part of the change, not an afterthought.

- Add a test that locks the behavior Characterization tests with small
  fixtures are ideal. Aim for invariants users care about (e.g., “no
  leftover control comments after stamping this pattern”). Name the test
  so a future reader can guess the scenario.

- Expect asynchronous review Many enterprise networks block GitHub
  notifications. If you don’t get a response within a few days, ping
  once more with a succinct summary and a link to your repro. Keep all
  context in the PR, not private chats.

# Agile and craftsmanship lens

- Small batch size by design: granular PRs reduce cycle time and make
  rollback safe.

- Intent‑revealing artifacts: issues and tests capture “why” and “what,”
  docs capture “how.”

- Shared vocabulary: reuse the names used in Javadoc and site docs;
  avoid synonyms that confuse future readers.

# Solo maintainer + enterprise usage angle

Enterprise contributors often work under constraints (timeboxed sprints,
restricted environments). This workflow honors that reality: it
maximizes the chance that your contribution lands and stays healthy. As
a solo maintainer, I commit to clear triage and honest timelines. When
I’m unavailable, a well‑documented PR with a small repro remains
reviewable by others later. The goal is to make contributions feel like
cooperation rather than a negotiation.

# Practical checklist for your PR

- [ ] One issue describing the problem and intent, with links to docs.

- [ ] One small repro (fixture or `.docx`) or a clear code snippet.

- [ ] One focused PR with tests and updated docs in the same change.

- [ ] CI is green (build + AsciiDoc + tests). If CI is red, add a note
  about what’s failing.

- [ ] Short description that explains the “why” in 2–3 sentences.

# References

- Docs: contribution guidelines in `CONTRIBUTING.adoc`; site docs under
  `engine/src/site/asciidoc/`.

- Code: tests under `engine/src/test/java/` for examples of
  invariant‑focused assertions.
