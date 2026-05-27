# Diagram Quality Gates

Use this reference whenever the output contains diagrams, flowcharts, architecture maps, codebase maps, dependency graphs, runtime flows, data flows, mind maps, or decision trees.

## Required diagram review loop

Before finalizing the HTML, review every diagram against these gates. Fix failures instead of explaining them away.

| Gate | Check | Pass condition |
|------|-------|----------------|
| Purpose | What question does this diagram answer? | The heading and intro sentence make the diagram's purpose clear. |
| Semantic spec | Is the meaning defined before drawing? | Purpose, nodes, edges, groups/layers, layout, visual grammar, and confidence are defined before SVG/HTML rendering. |
| Scope | Is the diagram too large? | Usually 5-9 primary nodes; split at 12+ nodes unless the grouping is very clear. |
| Labels | Can labels be read quickly? | Short node labels, no tiny text, no overlapping labels, readable in light and dark themes. |
| Edges | Are arrows understandable? | Arrows have clear direction, avoid unnecessary crossings, and do not imply certainty when inferred. |
| Legend | Are visual encodings explained? | Any color, line style, shape, badge, or confidence marker has a nearby legend or note. |
| Semantics | Are shapes and colors consistent? | Same kind of thing uses the same shape/color across all diagrams in the file. |
| Explanation | Does the reader know how to interpret it? | Add a short "How to read this" or takeaway note for non-obvious diagrams. |
| Accuracy | Is the diagram supported by evidence? | Facts are grounded in the provided content or inspected code; guesses are labeled as inferred. |
| Responsiveness | Does it fit common widths? | SVG uses `viewBox`, scales to container width, and does not clip important content. |
| Accessibility | Can assistive tech identify it? | Every SVG has `role="img"`, a descriptive `aria-label` or `aria-labelledby`, plus `<title>` and `<desc>` when practical. |

## Fix-first rules

If a diagram fails a gate, use the smallest correction that improves comprehension:

- Missing semantic spec → define purpose, nodes, edges, groups/layers, layout, visual grammar, and confidence before editing SVG.
- Too many nodes → split into layered diagrams or group nodes into subsystems.
- Crossed arrows → switch to a left-to-right, top-to-bottom, radial, or swimlane layout.
- Ambiguous arrows → label the edge or move detail into adjacent bullets.
- Too much text inside SVG → shorten SVG labels and put explanation in cards beside/below the diagram.
- Color-only meaning → add icons, labels, stroke styles, badges, or a legend.
- Uncertain runtime behavior → use dashed arrows and an "Inferred" note instead of presenting it as fact.
- Repeated visual patterns → extract a small CSS class, not a large diagram framework.

## Standard visual grammar

Use this default grammar unless the user's content suggests something clearer:

| Meaning | Shape/style |
|---------|-------------|
| User/person/team | Circle or rounded pill |
| App/service/module | Rounded rectangle |
| Package/folder/layer | Group boundary or container |
| Database/storage/cache | Cylinder-like shape or stacked rectangle |
| Queue/event stream | Horizontal pill with repeated tick marks |
| External system/API | Dashed border |
| Direct call/dependency | Solid arrow |
| Data movement | Blue or accent arrow with label |
| Inferred/optional path | Dashed arrow |
| Risk/error path | Red/orange stroke or badge |
| Important path | Thicker accent stroke, but use sparingly |

## Layout recipes

Choose the simplest layout that matches the idea:

- System context → center the system, place users/external systems around it.
- Layered architecture → stack layers vertically from interface to infrastructure.
- Runtime/request flow → left-to-right sequence with numbered steps.
- Data flow → source → transform/process → store → consumer.
- Dependency map → stable modules left, dependent modules right; keep arrows mostly one direction.
- Decision tree → top-down branches with short decision labels.
- Comparison → two or three aligned columns with shared row labels.
- Timeline/process → left-to-right for short sequences, vertical for long sequences.

## Anti-patterns

Avoid:

- One giant graph that tries to answer every question at once.
- Decorative arrows that do not represent a real relationship.
- Color palettes without meaning.
- Icons without labels.
- Tiny SVG text that looks fine only at full desktop width.
- Mixing dependency, runtime, data, and deployment flows in one diagram unless clearly separated by lanes.
- Mermaid for architecture/codebase diagrams unless the user explicitly asks for Mermaid.
- Auto-generated layout as a substitute for understanding the relationships first.
