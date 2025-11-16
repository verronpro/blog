# blog — Joseph Verron professional blog

Diagrams as Code pipeline
-------------------------

This site renders diagrams client‑side to stay compatible with GitHub Pages (no custom Jekyll plugins required).

Supported authoring patterns:

- Mermaid: fenced blocks
- PlantUML and Graphviz/DOT: rendered via Kroki (https://kroki.io) using client‑side compression.

How to write diagrams
1) Mermaid with fenced code (auto‑enhanced):
    ````markdown
    ```mermaid
    flowchart TD
      A --> B
    ```
    ````
2) PlantUML (via Kroki), fenced block (auto‑enhanced):
    ````markdown
    ```plantuml
    @startuml
    Alice -> Bob: Hello
    @enduml
    ```
    ````
3) Graphviz/DOT (via Kroki), fenced block (auto‑enhanced):

    ````markdown
    ```dot
    digraph G { A -> B }
    ```
    ````

Notes
- Rendering happens in the browser. Internet access to cdn.jsdelivr.net and kroki.io is required for full rendering.
- Dark mode is supported through CSS tokens; diagrams are embedded as SVGs where possible.
- Print styles collapse the two‑column layout to a single column.

Local development
- bundle install
- bundle exec jekyll serve --livereload

Production build
- PowerShell: `$env:JEKYLL_ENV = 'production'; bundle exec jekyll build`
