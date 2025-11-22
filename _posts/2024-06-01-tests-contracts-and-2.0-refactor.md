### Three editorial angles to choose from

1) Breaking for clarity: why a modular reorg beats slow drift
   - Focus: the decision to introduce `engine/`, move `DocxStamper`/`DocxStamperConfiguration`, and delete legacy APIs. Explain the trade-offs and how “clean seams” speed up future work.
   - Audience: maintainers and adopters evaluating upgrades.
2) Solo maintainer, enterprise users: reducing risk with explicit boundaries
   - Focus: how stable packages and smaller surfaces lower review cost and misuse risk. Emphasize migration guide, CI checks, and characterization tests.
   - Audience: teams bringing the library into regulated or security-conscious environments.
3) Towards 2.0: aligning APIs with the mental model
   - Focus: the `2.0-SNAPSHOT` pivot, intention-revealing namespaces, and how examples/tests were updated to match the conceptual model.
   - Audience: contributors and power users who care about API design and upgrade paths.

### Questions for your perspective and color
- What concrete pain pushed you over the edge to reorganize now? A PR that hurt, an on-call moment, or contributor confusion?
- Which deprecated types did you most happily delete, and why?
- For adopters: is the headline migration “old import → new import,” or are there subtle semantic changes worth flagging?
- What CI or test evidence gave you confidence after the moves (specific suites, mutation testing thresholds, link checks)?
- Any notable downstreams (companies, projects) you want to acknowledge or reassure?
- Do you want the tone to be more release‑note like, or more reflective (craftsmanship/architecture)?

Focus on the move to 2.0, with the target to provide intention revealing namespaces, that gives me more freedom to refactor the codebase in the core, vs the extension API that should stay as stable as possible.

The main drive behind this reorganization is the need to have a safe space (the core) to deeply refactor the inner algorithm of the library, that suffered several long-standing design issue, without breaking too much the API that users knows, and that attracted me to the library in the first place.

The deprecated types that I deleted or can now delete freely are all in the core package.

For adopters, I think that switching to new import should be mostly working fine, but I advice on also switching to the factory methods inside the preset package as much as possible, if they are not explicitly trying to extends the library.

All the test suites keeps running, the mutation testing values didn't greatly change, so it is sure nothing major broke.

I want the tone to be reflective on the practices of craftsmanship and software architecture.



### Draft article (~400 words) — Monthly Commit — Modular Reorg and Breaking Changes

```yaml
---
layout: article
title: "Monthly Commit — Modular Reorg and Breaking Changes"
date: 2024-06-01
categories: [office-stamper]
tags: [ office-stamper, java, api ]
author: Joseph
description: Introducing an engine module, intention‑revealing packages, and a leaner public surface on the path to 2.0.
---
```

Sustainable agility depends on boundaries you can explain and defend. In May I executed a deliberate reorganization: introduced an `engine` module, relocated core types to intention‑revealing packages, and removed legacy shims. These were breaking changes by design.

Highlights
- Relocated sources under `engine/` and `examples/` ([6bbcd21](https://github.com/verronpro/office-stamper/commit/6bbcd21)), adding a proper `engine/pom.xml` ([45d6fb8](https://github.com/verronpro/office-stamper/commit/45d6fb8)).
- Moved `DocxStamper` to `pro.verron.officestamper.core` ([bfa7877](https://github.com/verronpro/office-stamper/commit/bfa7877)).
- Moved `DocxStamperConfiguration` likewise and removed its deprecation ([5cb94a1](https://github.com/verronpro/office-stamper/commit/5cb94a1)).
- Bumped the engine to `2.0-SNAPSHOT` to signal the new baseline ([592bd73](https://github.com/verronpro/office-stamper/commit/592bd73)).

Why this matters
- Clear seams beat tacit conventions. Modules and packages are user‑facing design decisions—make them visible and stable.
- Smaller surfaces lower risk. Deleting deprecated branches reduces reviewer fatigue and the chance of misuse under time pressure.
- Docs-as-code alignment. Examples and docs now compile against the same API adopters should rely on.

Migration notes (old → new)
- Imports:
  - `org.wickedsource.docxstamper.DocxStamper` → `pro.verron.officestamper.core.DocxStamper`
  - `org.wickedsource.docxstamper.DocxStamperConfiguration` → `pro.verron.officestamper.core.DocxStamperConfiguration`
- Prefer the preset entry points when possible:
  - `OfficeStampers` and `OfficeStamperConfigurations` under `pro.verron.officestamper.preset.*`

Example before/after
```java
// before
import org.wickedsource.docxstamper.DocxStamper;
// after
import pro.verron.officestamper.core.DocxStamper;
```

Risk and mitigation
- Upgrade friction: provide a short mapping table (above) and keep semantics stable.
- Missed couplings: run the full suite; examples and site build now verify integration.
- Over‑deletion: stage removals; deprecate → migrate → delete across minor releases.

Impact
- Cleaner mental model; faster contributor onboarding; easier enterprise review of a focused `engine` artifact.

Next
- Stabilize the 2.0 public surface and expand migration snippets. If you maintain extensions, validate against `2.0-SNAPSHOT` and file issues early.

References: commits around 2024‑05‑14 → 2024‑05‑17; see `engine/pom.xml` and `pro.verron.officestamper.core.*` for the new anchors.
