## Mandatory HTML Skeleton

**EVERY visualization MUST start from this skeleton.** Copy it, then replace the content placeholder. The skeleton is intentionally simple: Bootstrap handles layout/components, while custom CSS stays small and focused on theme tokens, diagrams, print, and accessibility.

**Design philosophy: content-first, diagram-first, minimal styling.** Use Bootstrap CDN for grids, cards, tables, nav, accordions, tabs, badges, and responsive spacing. Add only the custom CSS needed for the specific visualization or inline SVG diagrams. Avoid decorative animation unless the user explicitly asks for it.

```html
<!DOCTYPE html>
<html lang="en" class="theme-light">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>YOUR TITLE HERE</title>

  <!-- Bootstrap: layout and common UI components -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">

  <!-- Optional: add language fonts only when needed -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">

  <!-- PNG export helper -->
  <script src="https://cdn.jsdelivr.net/npm/html-to-image@1.11.11/dist/html-to-image.js"></script>

  <!-- ADD OPTIONAL CDN LIBRARIES HERE, e.g. Chart.js. Avoid Mermaid unless the user asks. -->

  <style>
    /* ===== REQUIRED THEME TOKENS ===== */
    html.theme-light {
      --bg: #f8fafc;
      --surface: #ffffff;
      --surface-hover: #f1f5f9;
      --border: rgba(15, 23, 42, 0.12);
      --text: #0f172a;
      --text-secondary: #64748b;
      --accent: #2563eb;
      --accent-secondary: #7c3aed;
      --positive: #059669;
      --negative: #dc2626;
      --warning: #d97706;
    }

    html.theme-dark {
      --bg: #0f172a;
      --surface: #111827;
      --surface-hover: #1f2937;
      --border: rgba(148, 163, 184, 0.22);
      --text: #e5e7eb;
      --text-secondary: #94a3b8;
      --accent: #60a5fa;
      --accent-secondary: #a78bfa;
      --positive: #34d399;
      --negative: #fb7185;
      --warning: #fbbf24;
    }

    /* ===== BASE ===== */
    body {
      font-family: Inter, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      background: var(--bg);
      color: var(--text);
      line-height: 1.6;
      -webkit-font-smoothing: antialiased;
    }

    h1, h2, h3, h4, h5, h6 { color: var(--text); }
    p, li, td, th, label, span { color: inherit; }
    a { color: var(--accent); }
    .text-secondary { color: var(--text-secondary) !important; }

    .app-shell {
      max-width: 1180px;
      margin: 0 auto;
      padding: 32px 20px 56px;
    }

    .viz-card {
      background: var(--surface);
      border: 1px solid var(--border);
      border-radius: 16px;
      box-shadow: 0 10px 30px rgba(15, 23, 42, 0.06);
    }

    .theme-dark .viz-card {
      box-shadow: none;
    }

    .section-label {
      color: var(--accent);
      font-size: 0.8rem;
      font-weight: 700;
      letter-spacing: 0.08em;
      text-transform: uppercase;
    }

    .metric-value {
      color: var(--text);
      font-size: clamp(2rem, 5vw, 4rem);
      font-weight: 800;
      line-height: 1;
      letter-spacing: -0.04em;
    }

    .diagram-frame {
      background: var(--surface);
      border: 1px solid var(--border);
      border-radius: 16px;
      padding: 16px;
      overflow-x: auto;
    }

    svg text { fill: var(--text); }
    .svg-muted { fill: var(--text-secondary); color: var(--text-secondary); }
    .svg-node { fill: var(--surface); stroke: var(--border); }
    .svg-edge { stroke: var(--accent); stroke-width: 2; fill: none; }

    .viz-menu {
      position: fixed;
      top: 16px;
      right: 16px;
      z-index: 1030;
    }

    .viz-menu .btn,
    .viz-menu .dropdown-menu {
      border-color: var(--border);
    }

    .skip-link {
      position: absolute;
      left: -9999px;
      top: auto;
      width: 1px;
      height: 1px;
      overflow: hidden;
    }

    .skip-link:focus {
      position: fixed;
      left: 16px;
      top: 16px;
      width: auto;
      height: auto;
      z-index: 2000;
      padding: 8px 12px;
      border-radius: 8px;
      background: var(--accent);
      color: white;
      text-decoration: none;
    }

    @media (max-width: 768px) {
      .app-shell { padding: 24px 14px 40px; }
      .viz-menu { top: 10px; right: 10px; }
    }

    @media print {
      body { background: white !important; color: black !important; }
      .viz-menu, .skip-link { display: none !important; }
      .viz-card, .diagram-frame { border: 1px solid #ddd !important; box-shadow: none !important; }
      * { print-color-adjust: exact; -webkit-print-color-adjust: exact; }
    }

    /* ===== ADD VISUALIZATION-SPECIFIC STYLES BELOW ===== */
  </style>
</head>
<body>
  <a href="#main-content" class="skip-link">Skip to content</a>

  <!-- MENU: keep theme, PNG export, and print support -->
  <div class="viz-menu dropdown">
    <button class="btn btn-sm btn-outline-secondary bg-body dropdown-toggle" type="button" data-bs-toggle="dropdown" aria-expanded="false">
      Menu
    </button>
    <ul class="dropdown-menu dropdown-menu-end shadow-sm">
      <li><button class="dropdown-item" type="button" onclick="cycleTheme()"><span id="themeIcon">☀️</span> <span id="themeLabel">Light</span></button></li>
      <li><button class="dropdown-item" type="button" onclick="downloadImage()">📥 Download PNG</button></li>
      <li><button class="dropdown-item" type="button" onclick="window.print()">🖨️ Print / PDF</button></li>
    </ul>
  </div>

  <main id="main-content" class="app-shell" role="main">
    <!-- Replace this placeholder with semantic visualization content. -->
    <header class="mb-4">
      <p class="section-label mb-2">Visualization</p>
      <h1 class="display-5 fw-bold mb-3">YOUR TITLE HERE</h1>
      <p class="lead text-secondary mb-0">Write a clear one-paragraph summary that teaches the reader what this visual explains.</p>
    </header>

    <section class="viz-card p-4 mb-4" aria-labelledby="overview-heading">
      <h2 id="overview-heading" class="h4 mb-3">Overview</h2>
      <p class="mb-0">Replace this section with the most important insight, diagram, dashboard, or narrative block.</p>
    </section>

    <section class="diagram-frame mb-4" aria-labelledby="diagram-heading">
      <h2 id="diagram-heading" class="h4 mb-3">Diagram</h2>
      <!-- Prefer inline SVG/CSS diagrams for deterministic rendering. -->
      <svg viewBox="0 0 640 220" role="img" aria-label="Example diagram placeholder" class="w-100" style="max-height: 260px;">
        <rect x="40" y="70" width="150" height="80" rx="14" class="svg-node"></rect>
        <text x="115" y="115" text-anchor="middle" font-size="16" font-weight="700">Input</text>
        <path d="M 200 110 L 430 110" class="svg-edge"></path>
        <polygon points="430,110 414,101 414,119" fill="var(--accent)"></polygon>
        <rect x="450" y="70" width="150" height="80" rx="14" class="svg-node"></rect>
        <text x="525" y="115" text-anchor="middle" font-size="16" font-weight="700">Output</text>
      </svg>
    </section>
  </main>

  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
  <script>
    // === Theme: class-based, preserves .theme-light and .theme-dark ===
    var savedTheme = localStorage.getItem('viz-theme');
    var currentTheme = savedTheme || 'light';

    function applyTheme(theme) {
      currentTheme = theme;
      document.documentElement.className = 'theme-' + theme;
      document.documentElement.setAttribute('data-bs-theme', theme);
      localStorage.setItem('viz-theme', theme);

      var icon = document.getElementById('themeIcon');
      var label = document.getElementById('themeLabel');
      if (icon) icon.textContent = theme === 'dark' ? '🌙' : '☀️';
      if (label) label.textContent = theme === 'dark' ? 'Dark' : 'Light';

      if (typeof onThemeChange === 'function') onThemeChange(theme);
    }

    function cycleTheme() {
      applyTheme(currentTheme === 'dark' ? 'light' : 'dark');
    }

    applyTheme(currentTheme);

    // === Download PNG ===
    async function downloadImage() {
      var target = document.getElementById('main-content');
      if (!target || typeof htmlToImage === 'undefined') return;

      try {
        var url = await htmlToImage.toPng(target, { quality: 1, pixelRatio: 2, backgroundColor: getComputedStyle(document.body).backgroundColor });
        var link = document.createElement('a');
        link.href = url;
        link.download = document.title.trim().toLowerCase().replace(/[^a-z0-9]+/g, '-') + '.png';
        link.click();
      } catch (error) {
        console.error('Download failed:', error);
      }
    }

    // === Optional Chart.js hook ===
    // Define function onThemeChange(theme) {} when charts need theme-aware re-rendering.
  </script>
</body>
</html>
```

### Skeleton Rules

**Do:**
- Use Bootstrap layout/components first: `.container`, `.row`, `.col-*`, `.card`, `.table`, `.accordion`, `.nav-tabs`, `.badge`, `.alert`, and spacing utilities.
- Keep custom CSS small and specific to the visualization or diagram.
- Preserve class-based themes with both `.theme-light` and `.theme-dark`.
- Preserve these CSS custom property names: `--bg`, `--surface`, `--surface-hover`, `--border`, `--text`, `--text-secondary`, `--accent`, `--accent-secondary`, `--positive`, `--negative`, `--warning`.
- Preserve the menu with theme toggle, PNG download, and print/PDF actions.
- Keep exactly one `<main id="main-content">` and use multiple `<section>` blocks for major content.
- Prefer inline SVG/CSS for diagrams, especially architecture and codebase maps.
- Use complete explanatory sentences so the visual teaches the reader how to interpret it.
- Use `var` for top-level JavaScript variables to avoid hoisting/TDZ issues in generated HTML.

**Don't:**
- Don't add decorative animations, scroll reveals, counters, particles, glassmorphism, or heavy visual effects unless explicitly requested.
- Don't write large custom layout systems when Bootstrap utilities solve the layout.
- Don't use Tailwind utility-heavy markup in this Bootstrap skeleton.
- Don't use module syntax (`import`/`export`) inside generated HTML.
- Don't use Mermaid for architecture/codebase diagrams unless the user explicitly requests it.
- Don't create duplicate skip links or duplicate `<main id="main-content">` elements.
