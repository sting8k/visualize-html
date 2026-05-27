# Codebase Inline SVG/CSS Graphs

Use this reference when the user asks to visualize, understand, explain, map, or document a codebase/repository/system architecture with high-level diagrams.

Goal: produce layered high-level architecture graphs using inline SVG and CSS. This costs more code than Mermaid, but gives full rendering control, avoids parser/CDN failures, and prevents blank-page issues.

## When to use

Use this for prompts like:

- "visualize this codebase"
- "make architecture diagrams for this repo"
- "show high-level layers/modules"
- "draw graphs of the project"
- "explain the backend/frontend/data flow visually"
- "map dependencies/services/packages"
- "make diagrams for onboarding docs"

If the user mentions a repository, source tree, architecture, modules, services, layers, packages, entrypoints, runtime flow, dependencies, or onboarding, prefer this reference.

## Discovery workflow

Before drawing, inspect enough of the repo to avoid guessing. For large/codebase outputs, also follow `references/generation-protocol.md` so each diagram is written and verified as its own chunk:

1. Identify project type and package boundaries:
   - manifest files: `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `pom.xml`, `build.gradle`, `.sln`, etc.
   - top-level dirs and app/package folders.
2. Identify entrypoints:
   - CLI/main/server files, route registration, app bootstrap, workers, jobs, web entrypoints.
3. Identify boundary layers:
   - UI/API/CLI, application/service layer, domain/core, persistence, external integrations, infrastructure/config.
4. Identify dependency direction:
   - imports between modules, package references, service calls, queue/event producers and consumers.
5. Identify data/control flow:
   - request path, command path, job path, event path, DB/cache/object storage interactions.
6. Decide which diagrams answer the user's goal. Do not generate every possible diagram if a few strong diagrams are clearer.
7. For each selected diagram, write a semantic spec before drawing: purpose, nodes, edges, groups/layers, layout, visual grammar, and confidence.

Prefer `srcwalk`, `rg`, and package manifests for discovery. Keep diagrams high-level unless the user asks for file-level detail.

## Output structure

For a codebase visualization HTML, include:

1. Title and short repo summary.
2. A "How to read this" note explaining that diagrams are high-level and layered.
3. 3-6 semantic-first inline SVG/CSS diagrams, each written as its own chunk and containing:
   - a clear heading,
   - a one-sentence purpose,
   - a concise semantic spec or "How to read this" note,
   - the SVG graph,
   - 3-5 key takeaways.
4. Optional "open questions / confidence" section when parts are inferred.

Good default diagram set:

1. **System Context** — actors, external systems, main app boundaries.
2. **Layered Architecture** — UI/API/entrypoints → application/services → domain/core → persistence/infrastructure.
3. **Module / Package Map** — major directories/packages and dependency direction.
4. **Runtime Request Flow** — common request or command execution path.
5. **Data Flow** — databases, caches, queues, files, third-party APIs.
6. **Build / Deploy / Tooling Flow** — only if visible and relevant.

## Explanation style

Use a pedagogical, onboarding-friendly voice. The visualization should teach the reader how to understand the system, not just drop labels on a page.

- Use clear section titles that say what the view explains.
- Add a short "why this matters" sentence before or after each diagram.
- Use complete sentences in takeaways; avoid terse fragments like "API -> service -> DB" without explanation.
- Explain arrows and boundaries in plain language.
- Prefer terms a new teammate can understand; define project-specific jargon briefly.
- When confidence is low, say what evidence supports the inference and what remains unknown.

## SVG/CSS diagram principles

- Use inline `<svg>` inside semantic `<section>` blocks. Do not rely on external graph renderers.
- Treat the semantic spec as the source of truth; the SVG is a rendering of that spec, not the place where meaning is invented.
- Keep each graph readable: usually 5-15 nodes. Split larger graphs into multiple views.
- Use `viewBox` so diagrams scale responsively.
- Set `role="img"` and a descriptive `aria-labelledby` or `aria-label` on every SVG.
- Use `<title>` and `<desc>` inside each SVG for accessibility.
- Use `<defs><marker>` arrowheads instead of drawing arrowheads manually.
- Use CSS classes for nodes, groups, labels, arrows, and confidence markers.
- Keep SVG text short. Put details in adjacent takeaways/cards.
- Prefer simple deterministic layouts over clever auto-layout.
- After drafting each diagram, apply `references/diagram-quality.md` and fix readability, arrow, legend, responsiveness, accessibility, and confidence issues before finalizing.

## Shared SVG styling pattern

Add this kind of CSS to the generated HTML. Adjust colors via existing theme variables.

```css
.arch-diagram {
  width: 100%;
  overflow-x: auto;
  border: 1px solid var(--border);
  border-radius: 20px;
  background: linear-gradient(180deg, color-mix(in srgb, var(--surface) 92%, var(--accent) 8%), var(--surface));
  padding: 16px;
}

.arch-diagram svg {
  width: 100%;
  min-width: 760px;
  height: auto;
  display: block;
}

.node rect,
.node path,
.node circle {
  fill: color-mix(in srgb, var(--surface) 82%, var(--accent) 18%);
  stroke: var(--border);
  stroke-width: 1.5;
  filter: drop-shadow(0 10px 22px rgba(0, 0, 0, .18));
}

.node.core rect,
.node.core path {
  fill: color-mix(in srgb, var(--accent) 24%, var(--surface) 76%);
  stroke: var(--accent);
}

.node.external rect,
.node.external path {
  fill: color-mix(in srgb, var(--warning) 16%, var(--surface) 84%);
}

.node.storage rect,
.node.storage path {
  fill: color-mix(in srgb, var(--positive) 16%, var(--surface) 84%);
}

.node text {
  fill: var(--text);
  font: 600 14px/1.2 Inter, system-ui, sans-serif;
  text-anchor: middle;
  dominant-baseline: middle;
  pointer-events: none;
}

.node .subtext {
  fill: var(--text-secondary);
  font-size: 11px;
  font-weight: 500;
}

.edge {
  fill: none;
  stroke: color-mix(in srgb, var(--text-secondary) 70%, var(--accent) 30%);
  stroke-width: 2;
  marker-end: url(#arrow);
}

.edge.strong {
  stroke: var(--accent);
  stroke-width: 2.75;
}

.edge.dashed {
  stroke-dasharray: 7 6;
}

.edge-label {
  fill: var(--text-secondary);
  font: 600 11px/1 Inter, system-ui, sans-serif;
  text-anchor: middle;
}

.layer-band {
  fill: color-mix(in srgb, var(--surface-hover) 80%, transparent);
  stroke: var(--border);
  stroke-dasharray: 6 6;
  rx: 18;
}

.layer-title {
  fill: var(--text-secondary);
  font: 700 12px/1 Inter, system-ui, sans-serif;
  letter-spacing: .08em;
  text-transform: uppercase;
}
```

## Reusable SVG building blocks

### Marker and title/description

Put definitions inside each SVG so the graph remains self-contained.

```html
<svg viewBox="0 0 1000 520" role="img" aria-labelledby="ctx-title ctx-desc">
  <title id="ctx-title">System context diagram</title>
  <desc id="ctx-desc">High-level actors, application boundary, and external systems.</desc>
  <defs>
    <marker id="arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto-start-reverse">
      <path d="M 0 0 L 10 5 L 0 10 z" fill="currentColor"></path>
    </marker>
  </defs>
  <!-- graph content -->
</svg>
```

### Node pattern

Use groups with `transform` so nodes are easy to move.

```html
<g class="node core" transform="translate(430 210)">
  <rect x="-110" y="-42" width="220" height="84" rx="18"></rect>
  <text y="-8">Main Application</text>
  <text class="subtext" y="16">API + services</text>
</g>
```

### Edge pattern

Use paths for flexible routing. Label arrows only when it clarifies meaning.

```html
<path class="edge strong" d="M 250 210 C 310 210, 330 210, 370 210"></path>
<text class="edge-label" x="310" y="196">requests</text>
```

## Diagram-specific patterns

### 1. System context

Use when showing the repo's place in the outside world.

Layout:

- external actors on the left,
- app/system boundary in the center,
- external systems/storage on the right,
- strongest request path highlighted.

Good nodes:

- User / Client
- Admin / Operator
- Main Application
- Database
- External API
- Queue / Object Storage

### 2. Layered architecture

Use horizontal bands or vertical columns for layers.

Recommended vertical layout:

1. Interface layer
2. Application/use-case layer
3. Domain/core layer
4. Infrastructure/persistence layer

Use dashed layer rectangles behind nodes. Arrows should mostly point downward. If an infrastructure module calls upward, highlight it as unusual or inferred.

### 3. Module/package dependency map

Use grouped boxes for major top-level directories/packages.

Rules:

- Do not include every file.
- Show dependency direction with arrows.
- Use color or badges for app/package/tooling/external.
- Include a small legend if edge types differ: imports, calls, configures, emits.

### 4. Runtime request or command flow

Use a left-to-right pipeline with numbered steps. SVG is better than sequence syntax when you need full styling control.

Pattern:

- Client/Input → Router/Command → Validator → Service/Use Case → Repository/Gateway → DB/External → Response/Event
- Use step numbers as small circles.
- Use dashed arrows for optional/cache/async paths.

### 5. Data flow

Use storage-shaped nodes for databases/caches/queues. In SVG, approximate storage with rounded boxes or cylinders using paths; keep them simple.

Show:

- reads/writes separately if important,
- async queue/event paths dashed,
- external API calls clearly outside the main app boundary.

### 6. Build/deploy/tooling flow

Only include if visible from repo files. Show source → build/test → artifact/image → deploy target. Mark inferred deploy pieces clearly.

## Shape recipes

### Rounded service box

```html
<g class="node" transform="translate(200 120)">
  <rect x="-90" y="-34" width="180" height="68" rx="16"></rect>
  <text>Service</text>
</g>
```

### Database/storage cylinder approximation

```html
<g class="node storage" transform="translate(780 250)">
  <path d="M -80 -26 C -80 -48 80 -48 80 -26 L 80 30 C 80 52 -80 52 -80 30 Z"></path>
  <path d="M -80 -26 C -80 -4 80 -4 80 -26" fill="none" stroke="var(--border)" stroke-width="1.5"></path>
  <text y="6">Database</text>
</g>
```

### Boundary box

```html
<rect class="layer-band" x="280" y="70" width="440" height="360" rx="22"></rect>
<text class="layer-title" x="304" y="100">Application Boundary</text>
```

## Accuracy and confidence

Do not present guesses as facts.

Use wording like:

- "Based on the manifest and top-level folders..."
- "From import paths, this appears to be..."
- "Inferred because no explicit deploy config was found..."

When confidence is low:

- mark the diagram as "inferred",
- include an "Open questions" card,
- keep arrows generic instead of inventing exact runtime calls.

## Anti-patterns

Avoid:

- Mermaid or any external graph renderer for codebase architecture diagrams, unless the user explicitly requests it.
- One giant all-files graph.
- Diagrams with 30+ nodes in a single view.
- Mixing runtime flow, dependency flow, and data flow into one confusing graph.
- Pretty but inaccurate arrows.
- Copying directory names without explaining why they matter.
- Auto-layout code that is harder to debug than fixed SVG coordinates.
