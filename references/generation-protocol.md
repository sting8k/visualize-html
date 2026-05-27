# Generation Protocol — Chunked HTML and Semantic-First Diagrams

Use this reference for large HTML outputs, codebase visualizations, or any visualization with 3+ major sections or 2+ diagrams.

## Why this exists

Long single-write HTML generation is fragile. If the network, shell, or tool call fails, the whole file can be lost or corrupted. Large visualizations should be written incrementally and verified as they are built.

Diagram quality is also semantic-first: the diagram must explain the right idea before it looks polished. Use visual style to clarify meaning, not to decorate uncertainty.

## Chunked writing protocol

For generated HTML larger than about 15 KB, or containing multiple diagrams:

1. Write the skeleton and shared CSS/JS first.
2. Add one major section per chunk.
3. Use `Chunk N = Diagram N` for diagram-heavy files.
4. Verify file existence, size growth, and critical markers after each chunk.
5. Keep the file valid enough to recover after each chunk when practical.
6. Only run final browser/open step after all chunks pass validation.

Recommended chunk order for codebase/architecture visualizations:

```text
Chunk 1: HTML head, shared CSS, menu, opening <main>, summary placeholders
Chunk 2: intro / how-to-read / legend / metrics
Chunk 3: Diagram 1 section
Chunk 4: Diagram 2 section
Chunk 5: Diagram 3 section
Chunk N: Diagram N section
Chunk N+1: evidence, confidence, open questions
Chunk N+2: shared scripts, closing </main>, </body>, </html>
Chunk N+3: validation and browser open
```

## Section markers

Use explicit section markers in the HTML when building incrementally:

```html
<!-- SECTION: context START -->
<section id="context">...</section>
<!-- SECTION: context END -->
```

For unfinished files, placeholders are allowed temporarily, but final output must not contain unused placeholders or lorem ipsum.

## Post-chunk checks

After each chunk, run a small check appropriate to the current stage. Examples:

```bash
python3 - <<'PY'
from pathlib import Path
p = Path('/tmp/example.html')
print('exists:', p.exists())
print('size:', p.stat().st_size if p.exists() else 0)
PY
```

Final validation should check at least:

- exactly one `<main id="main-content">`,
- exactly one skip link,
- both `.theme-light` and `.theme-dark`,
- required theme CSS variables,
- menu actions: theme toggle, PNG download, print/PDF,
- closing `</html>`,
- every SVG has `viewBox` and `role="img"`,
- every diagram has a heading and purpose sentence,
- no unused placeholders remain.

## Semantic-first diagram protocol

Before writing each diagram's SVG/HTML, define the diagram semantics in prose or an HTML comment. This is the source of truth.

Minimum diagram spec:

```text
Diagram purpose: What question does this answer?
Nodes: main entities only, grouped by type/layer.
Edges: relationship type and direction.
Layout: system context, layered, sequence, data flow, dependency map, etc.
Visual grammar: shape/color/line meanings.
Confidence: fact, inferred, optional, or unknown.
```

Then render the diagram from that spec using inline SVG/CSS or simple Bootstrap/HTML layout.

## Diagram representation choice

Default choice: **inline SVG/CSS with fixed, deterministic layout**.

Use inline SVG/CSS when:

- the diagram needs arrows, groups, boundaries, confidence markers, or accessibility metadata,
- the output is a codebase, architecture, runtime, data-flow, dependency, or process map,
- semantic accuracy matters more than automatic layout.

Use simple Bootstrap/HTML layout when:

- the visual is mostly cards, tables, timelines, comparison columns, or KPI blocks,
- arrows are not central to comprehension.

Use Chart.js only for real numeric charts.

Avoid Mermaid unless the user explicitly requests it. Mermaid is compact, but it is less suitable here because syntax errors break rendering, layout is opaque, accessibility/control are weaker, and agent-generated graph syntax often encodes labels/edges less precisely than hand-authored SVG.

Avoid D3/Graphviz/Viz.js unless the user explicitly requests generated graph layout. They add dependency and complexity; they are useful for many-node graph layout, but they reduce deterministic reviewability in single-file HTML.

## Recovery rule

If generation is interrupted:

1. Inspect the existing file size and tail.
2. Resume from the last complete `<!-- SECTION: ... END -->` marker.
3. Do not rewrite completed chunks unless they are wrong.
4. Re-run final validation after closing the file.
