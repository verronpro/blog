---
layout: article
title:  "Diagrams as Code: Practical Benefits in Real-World Projects"
date:   2023-09-01 09:00:00
categories: [plantuml, graphviz, developerexperience, documentation]
author: Joseph
tags: [diagram-as-code, plantuml, graphviz]
description: "Why diagrams‑as‑code scales better than drag‑and‑drop tools, with PlantUML and Graphviz examples you can embed and version alongside code."
---

In several of my projects, I’ve integrated **Graphviz** and **PlantUML** into the development and documentation workflows. These tools allow diagrams to be described and stored in plain text format — a practice often referred to as *Diagrams as Code*.

While traditional tools like draw\.io or Visio are familiar and visual, they don’t scale well in collaborative environments. In contrast, diagram DSLs (domain-specific languages) make it possible to treat visuals like any other code artifact: version-controlled, diffable, and automation-friendly.

---

## Why Diagrams as Code?

There are a few recurring reasons why I now prefer this approach:

* **Maintainability**: When diagrams are stored as text, they can be edited quickly without manually repositioning elements.
* **Automation**: I integrate diagram generation into CI pipelines and IDE plugins to ensure documentation stays up to date.
* **Traceability**: Changes to diagrams are tracked in Git alongside the code they describe.
* **Developer proximity**: Keeping diagrams close to the code encourages developers to maintain them.

---

## Example: Gantt Diagram in PlantUML

In a recent internal IT project, we used a PlantUML Gantt diagram to track the onboarding process for new employees across multiple teams.

Here's a simplified snippet:

```plantuml
@startgantt
Project starts the 2025/05/01

[Hardware setup] starts at 2025/05/01 and lasts 5 days
[Account creation] starts at 2025/05/01 and lasts 2 days
[Training sessions] starts after [Account creation]'s end and lasts 3 days
[Welcome meeting] starts at 2025/05/06

@endgantt
```

This short description produces a visual timeline we can embed in internal docs or onboarding pages. Because the source is plain text, it’s easy to adjust dates or durations — no dragging and resizing needed.

---

## Example: Class Diagram in Graphviz

In another project, we used Graphviz to quickly sketch out the core relationships of a Java domain model before starting implementation.

```dot
digraph classes {
  node [shape=box];

  User -> Account
  Account -> Subscription
  User -> Preferences
}
```

This quick sketch helped align the team during sprint planning, and the DOT file was committed next to the design document. As the domain evolved, we updated the graph just like any other source file.

---

## Team Adoption

Initial reactions to text-based diagramming were mixed. Some team members were hesitant due to the unfamiliar syntax. But over time, most appreciated the gains:

* Easy to grep/search in a large codebase
* Merge conflicts are easier to handle than binary diagram formats
* Enables “documentation as living code,” rather than a separate, neglected deliverable

---

## Conclusion

Diagrams as Code improve clarity without adding friction. Whether you’re working on architecture, onboarding workflows, or software design, they offer a sustainable way to document and communicate structure.

If your team struggles to keep documentation aligned with implementation, this approach may be worth adopting — even gradually.
