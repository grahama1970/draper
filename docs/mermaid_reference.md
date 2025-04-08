# Mermaid.js Comprehensive Reference

_Summarized from https://mermaid.js.org and related sources_

---

## Supported Diagram Types

- **Flowcharts**: Workflows, processes, directional (TB, LR, BT, RL)
- **Sequence Diagrams**: Interactions over time
- **Gantt Charts**: Project timelines
- **Class Diagrams**: UML classes and relationships
- **State Diagrams**: States and transitions
- **Entity-Relationship Diagrams (ERDs)**: Database entities (experimental)
- **Git Graphs**: Git workflows and branches
- **User Journey Diagrams**: User experience flows
- **Pie Charts**: Data visualization
- **Requirement Diagrams**: System and requirement relations
- **Quadrant and XY Charts**: Categorization, scatterplots

---

## General Syntax Rules

- **Identifiers**: Alphanumeric, underscores, dashes. Reserved words like `end` must be capitalized.
- **Directions**: TB, LR, BT, RL
- **Text/Labels**: Enclose in double quotes `"..."`. Supports Markdown/HTML (limited).
- **Styling**: `style`, `classDef`, themes, CSS
- **Arrows**: `-->`, `--o`, `--x`, with spacing rules
- **No `<br/>` tags**: Use `\n` for multiline labels.

---

## Initialization & API

- `mermaid.initialize()` — global config
- `mermaid.render(id, definition)` — render diagram
- `mermaid.parse()` — validate syntax

---

## Diagram-Specific Features

### Flowcharts
- Nodes: processes, decisions, data
- Subgraphs supported

### Sequence Diagrams
- Participants, messages, activation bars
- Loops, conditionals, notes

### Gantt Charts
- Tasks: start date, duration, progress
- Grouping via `section`

### Class Diagrams
- Classes with attributes/methods
- UML relationships (`<|--`, `*--`, etc.)

### State Diagrams
- States, transitions, initial/final states

### ERDs
- Entities, relationships, cardinality

---

## Limitations

- Complex layouts can be hard to customize
- ERDs are experimental
- Only predefined shapes/relationships
- Large diagrams may be hard to manage

---

## References
- https://mermaid.js.org
- https://swimm.io/learn/mermaid-js/mermaid-js-a-complete-guide
- https://freecodecamp.org/news/use-mermaid-javascript-library-to-create-flowcharts/
- https://squidfunk.github.io/mkdocs-material/reference/diagrams/
- https://ckeditor.com/blog/basic-overview-of-creating-flowcharts-using-mermaid/