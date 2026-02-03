# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **documentation-only** project providing a migration guide from the Kubernetes community NGINX Ingress Controller ([`kubernetes/ingress-nginx`](https://github.com/kubernetes/ingress-nginx)) to the Official NGINX Ingress Controller ([`nginx/kubernetes-ingress`](https://github.com/nginx/kubernetes-ingress)). It is owned by F5, Inc. and licensed under Apache 2.0.

There is no build system, test framework, or package manager. The project consists entirely of Markdown documentation and a self-contained HTML page.

## Hosting

- **Repository**: https://github.com/alessfg/nginx-ingress-migration-guide
- **GitHub Pages**: https://alessfg.github.io/nginx-ingress-migration-guide/
- GitHub Pages serves from the `main` branch root (`/`), rendering `index.html` (the latest version, currently v8)

## Key Files

- `index.html` — **The live interactive guide** served by GitHub Pages. Self-contained HTML (no external dependencies, no build step). Currently equivalent to v8.
- `nginx-ingress-migration-guide.md` — The original Markdown guide (~2,170 lines) with annotation mapping tables and CRD examples. Serves as reference material.
- `nginx-ingress-migration-guide.html` — v1: Separate expandable CRD examples section with search.
- `nginx-ingress-migration-guide-v2.html` — v2: Inline expandable table rows combining mappings and CRD examples.
- `nginx-ingress-migration-guide-v3.html` — v3: Grouped related annotations into single expandable rows; added CRD install instructions per dropdown.
- `nginx-ingress-migration-guide-v4.html` — v4: Split into OSS/Plus sections; alphabetical ordering; expanded CRD Features Summary; "How to Read the Mappings" guide.
- `nginx-ingress-migration-guide-v5.html` — v5: All annotations from markdown guide; expandable examples for every row; dual Annotation/CRD tabs for rewrites, rate limiting, basic auth; improved search filtering that hides entire categories; GitHub contribution link.
- `nginx-ingress-migration-guide-v6.html` — v6: Anchor IDs on all h2/h3 headings with hover-to-reveal permalink icons; badge flairs for Annotation, ConfigMap, and all CRD types; full `nginx.ingress.kubernetes.io/` prefix on community annotations; consistent 50% column widths; Route Delegation category for VirtualServerRoute CRD.
- `nginx-ingress-migration-guide-v7.html` — v7: Interactive YAML Migration Analyzer; paste a community NGINX Ingress YAML manifest to get migration suggestions with color-coded result cards, summary pills, section deep-links with highlight, and sample YAML loader. 52-entry annotation mapping database with O(1) lookup. Safe DOM rendering (no innerHTML).
- `nginx-ingress-migration-guide-v8.html` — v8: Config-specific migration suggestions; YAML Analyzer now produces three numbered steps: (1) Annotation Swaps with side-by-side before/after comparison and diff-highlighted annotation lines, (2) ConfigMap Changes with side-by-side comparison, (3) CRD Resources grouped by kind with install commands and generated CRD YAML using the user's actual values. Each step has Copy buttons, step count badges, and collapsible detail cards. `officialMapping` data model on all 52 entries with transform types (direct, booleanInvert, appendRateUnit, appendTimeUnit, appendBufferSize, snippetWrap, corsSnippet, proxyRedirect). 13+ CRD generator functions produce valid YAML from user values. CORS and proxy-redirect generate full snippet YAML from user values. Dual-approach entries appear in both Step 1 and Step 3. YAML syntax highlighting with color-coded keys, values, comments, keywords, booleans, numbers, separators, and anchors. Dark mode toggle with localStorage persistence and full dark-mode CSS. Drag-and-drop YAML file upload. Live input status (line count + annotation count). Keyboard shortcuts (Ctrl+Enter to analyze, Tab inserts 2 spaces). Annotation hover tooltips (`ANNOTATION_TIPS` with 50+ entries). Migration complexity indicator (Simple/Moderate/Advanced with dot badges). Success banner with step summary. Fade-in animations on result sections. Active nav highlighting via IntersectionObserver. Nav badge showing annotation count. Collapsible long YAML blocks (>12 lines) with gradient fade. "Copy All Migration YAML" and "Download YAML" export buttons. "What's Next?" contextual links section. "Edit YAML & Re-Analyze" button. Empty state with file icon and drag-and-drop hint. Print styles hiding interactive elements. Responsive mobile layout. ~3,750 lines total.

## Key Domain Concepts

- **Annotation prefix difference**: Community uses `nginx.ingress.kubernetes.io/`, Official uses `nginx.org/` (open source) or `nginx.com/` (NGINX Plus)
- **CRD support**: The Official controller supports VirtualServer, VirtualServerRoute, Policy, TransportServer, and GlobalConfiguration CRDs
- **NGINX Plus**: Only the Official controller supports NGINX Plus features (session affinity, JWT, OIDC, API key auth, WAF)
- **Naming**: Do not refer to "NGINX Inc." — use "Official NGINX" or "Official NGINX Ingress Controller"
- **Mapping types**:
  - Annotation-to-annotation mappings (expandable rows with before/after Ingress YAML)
  - CRD-based mappings where multiple community annotations consolidate into a single CRD (expandable rows with install-box)
  - Dual-approach mappings where an annotation maps to either an annotation OR a CRD (expandable rows with tabbed interface)
  - Plus-only features with no community equivalent
  - Official-only features with no community equivalent (marked N/A on left column)

## HTML Structure and Conventions

The HTML guide (`index.html`) follows these patterns:

- **Self-contained**: All CSS and JavaScript are inline. No external dependencies.
- **Sections**: YAML Analyzer (v7+), Key Differences, OSS Mappings (22 categories incl. Route Delegation), Plus Mappings (6 categories), CRD Features Summary, Installing CRDs, Migration Checklist, Additional Resources.
- **Table pattern**: Each category uses `<table class="mapping-table">` with two columns: community annotation → official equivalent.
- **Expandable rows**: CRD-based mappings use paired rows:
  ```html
  <tr class="expandable" onclick="toggleRow(this)">
      <td><div class="annotation-list"><code>nginx.ingress.kubernetes.io/annotation-1</code><code>nginx.ingress.kubernetes.io/annotation-2</code></div></td>
      <td><span class="badge badge-policy">Policy CRD</span> <code>crdField</code></td>
  </tr>
  <tr class="example-row"><td colspan="2"><div class="example-content">
      <!-- install-box, comparison blocks, info-box -->
  </div></td></tr>
  ```
- **Grouped annotations**: Related annotations (e.g., all `session-cookie-*`) are listed vertically in `<div class="annotation-list">`.
- **Install boxes**: Each CRD dropdown includes a `.install-box` with `kubectl apply` commands and a copy button.
- **Badge classes**: `.badge-annotation`, `.badge-configmap`, `.badge-virtualserver`, `.badge-virtualserverroute`, `.badge-policy`, `.badge-transportserver`, `.badge-globalconfiguration`, `.badge-plus`. CRD badge text includes "CRD" suffix (e.g., "Policy CRD").
- **Comparison blocks**: Side-by-side YAML using `.comparison > .comparison-block.old` / `.comparison-block.new`.
- **Alphabetical ordering**: Categories within each section (OSS/Plus) and annotations within grouped rows are sorted alphabetically.
- **Dual-example tabs**: Where a community annotation maps to either an annotation or a CRD, the example row uses a tabbed interface:
  ```html
  <div class="approach-tabs">
      <button class="approach-tab active" onclick="switchApproach(this, 'annotation')">Annotation Approach</button>
      <button class="approach-tab" onclick="switchApproach(this, 'crd')">CRD Approach</button>
  </div>
  <div class="approach-content active" data-approach="annotation">...</div>
  <div class="approach-content" data-approach="crd">...</div>
  ```
  The CRD tab includes the `.install-box`; the annotation tab does not. Currently used in: Authentication (Basic), Rate Limiting, Rewrites.
- **GitHub contribution link**: Present in the header (button with GitHub SVG icon), Additional Resources list, and footer. Points to `https://github.com/alessfg/nginx-ingress-migration-guide`.
- **YAML Analyzer** (v7+): `<section id="analyzer">` with drag-and-drop zone (`#dropZone`), `<textarea id="yamlInput">` (dark theme), three action buttons (Analyze YAML, Clear, Load Sample), live input status bar (`#inputStatus` showing line count + annotation count), keyboard shortcut hint, and `<div id="analyzerResults">`. Results are built via DOM methods: summary pills (`.analyzer-pill.found/.paths/.crds/.unrecognized`) with click-to-scroll, success banner with complexity indicator (Simple/Moderate/Advanced), three numbered steps (`.analyzer-step`) with step count badges, side-by-side before/after comparison grids (`.analyzer-comparison`) with diff-highlighted annotation lines (`.yaml-diff-line.removed/.added`), collapsible long YAML blocks, and unrecognized section. YAML blocks use syntax highlighting via `highlightYaml()` with CSS classes: `.yaml-key`, `.yaml-value`, `.yaml-comment`, `.yaml-keyword`, `.yaml-bool`, `.yaml-number`, `.yaml-separator`, `.yaml-anchor`. Export row with "Copy All Migration YAML" (`.analyzer-copy-all`) and "Download YAML" (`.analyzer-download-btn`) buttons. "What's Next?" section with contextual links. "Edit YAML & Re-Analyze" button scrolls back to textarea. Annotation hover tooltips (`ANNOTATION_TIPS` with 50+ entries, CSS-only via `::after` pseudo-element). Empty state with file icon and hints. Fade-in animations (`@keyframes analyzerFadeIn`). Nav badge showing annotation count after analysis. The JS mapping database (`ANNOTATION_MAPPINGS`) contains 52 entries covering all community annotations, with `ANNOTATION_LOOKUP` Map for O(1) access by short annotation name.
- **Dark mode**: Toggle button in header (`#darkToggle`), `body.dark-mode` class with full CSS overrides for all components. Persists via `localStorage.setItem('darkMode')`. Sun/moon icon swap via DOM SVG manipulation.
- **Active nav highlighting**: `IntersectionObserver` marks the current section's nav link as `.active` while scrolling.
- **Print styles**: `@media print` block hides interactive elements (nav, actions, buttons, empty state), removes shadows, adjusts YAML block colors for readability.
- **Responsive styles**: `@media (max-width: 600px)` stacks analyzer actions, full-width buttons, adjusted step header layout.

## Markdown Guide Structure

The migration guide (`nginx-ingress-migration-guide.md`) is organized as:

1. **Key Differences table** — high-level comparison of the two controllers
2. **Annotation Mapping Tables** — grouped by feature category (14 categories), each with a two-column table: community annotation → official annotation/CRD
3. **CRD-Based Migration Examples** — YAML before/after examples for features that require CRDs (SSL passthrough, mTLS, upstream config, health checks, rewrites, redirects, auth, rate limiting, canary, access control, headers, error handling, WAF, caching, gRPC, traffic splitting, VirtualServerRoute delegation, CORS)
4. **CRD-Only Features** — tables of VirtualServer, Policy, TransportServer, and GlobalConfiguration features with no community annotation equivalent
5. **TransportServer Examples** — TCP and UDP load balancing
6. **Installing CRDs** — kubectl commands for CRD installation
7. **Quick Reference** — concise annotation-to-CRD mapping cheat sheet
8. **Migration Checklist** — step-by-step migration steps
9. **Additional Resources** — links to official docs

## Research Approach

When updating or verifying annotation mappings, use the GitHub MCP tools or API access to fetch source documentation directly from the official documentation and source repositories:

- **Community controller annotations**: `kubernetes/ingress-nginx` at `docs/user-guide/nginx-configuration/annotations.md`
  - GitHub: https://github.com/kubernetes/ingress-nginx/blob/main/docs/user-guide/nginx-configuration/annotations.md
  - Published docs: https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/
- **Official controller annotations**: `nginx/kubernetes-ingress` — check `docs/content/` directory tree for annotation and CRD documentation
  - GitHub: https://github.com/nginx/kubernetes-ingress/tree/main/docs/content
  - Annotations: https://docs.nginx.com/nginx-ingress-controller/configuration/ingress-resources/advanced-configuration-with-annotations/
  - VirtualServer/VirtualServerRoute CRD: https://docs.nginx.com/nginx-ingress-controller/configuration/virtualserver-and-virtualserverroute-resources/
  - Policy CRD: https://docs.nginx.com/nginx-ingress-controller/configuration/policy-resource/
  - TransportServer CRD: https://docs.nginx.com/nginx-ingress-controller/configuration/transportserver-resource/
  - GlobalConfiguration CRD: https://docs.nginx.com/nginx-ingress-controller/configuration/global-configuration/globalconfiguration-resource/
- **Official migration guide**: https://docs.nginx.com/nginx-ingress-controller/install/migrate-ingress-nginx — F5/NGINX's own migration documentation from the community controller
- **Avoid WebFetch** for external documentation sites; prefer GitHub MCP tools or API access to raw source docs in the repos

## Design Decisions (History)

These decisions were made iteratively during development and should be preserved:

1. CRD examples are **inline** (expandable table rows), not in a separate section.
2. Related annotations are **grouped** into single expandable rows (e.g., all `session-cookie-*` annotations in one row).
3. Each CRD dropdown includes **installation instructions** (`kubectl apply` commands) specific to the CRDs needed.
4. Mappings are **split into OSS and Plus** sections to distinguish what requires a Plus subscription.
5. All categories and annotations are in **alphabetical order**.
6. A **"How to Read the Mappings"** box explains the row types (annotation-to-annotation, CRD-based, grouped, official-only).
7. The CRD Features Summary includes **descriptions and context** for each CRD type, not just bullet lists.
8. **Every row is expandable** (v5+) — annotation-to-annotation rows show before/after YAML; CRD rows show install-box + CRD YAML; dual-approach rows use tabs.
9. **Search hides entire categories** when no rows match, and collapses expanded example rows during search.
10. A **GitHub contribution link** appears in the header, Additional Resources, and footer.
11. All `<h2>` and `<h3>` headings have **`id` attributes** for direct linking, with **hover-to-reveal permalink icons** (GitHub-style chain-link SVG, injected via JS using safe DOM methods).
12. **Badge flairs** mark the mapping type in the right column: `Annotation` (teal), `ConfigMap` (yellow), `VirtualServer CRD` (blue), `VirtualServerRoute CRD` (indigo), `Policy CRD` (purple), `TransportServer CRD` (orange), `GlobalConfiguration CRD` (brown), `Plus Required` (orange). Each CRD type has its own distinct badge class and color.
13. Community annotations display the **full `nginx.ingress.kubernetes.io/` prefix** in the left column for clarity.
14. Mapping table columns use **`width: 50%; white-space: nowrap`** for consistent alignment across all categories.
15. **Route Delegation** category added to OSS Mappings for VirtualServerRoute CRD (N/A on community side).
16. **YAML Migration Analyzer** (v7+) is the first section, before Key Differences. Uses a `const ANNOTATION_MAPPINGS` array (52 entries) with a `Map`-based `ANNOTATION_LOOKUP` for O(1) annotation resolution. The YAML parser is line-based (no external library), handles multi-document YAML (`---`), inline annotations, quoted keys/values, and comments. Results are rendered using safe DOM methods (`createElement`/`textContent`) — no `innerHTML`. Summary pills show annotation count, migration paths, CRD requirements, and unrecognized counts. "View full migration example" links scroll to the relevant section anchor and apply a 2-second yellow highlight via `highlightSection()`.
17. **Stepped Migration Output** (v8+) replaces flat card list with three numbered steps: Step 1 (Annotation Swaps) generates a copy-ready `annotations:` block with translated values; Step 2 (ConfigMap Changes) generates a ConfigMap YAML snippet; Step 3 (CRD Resources) groups by CRD kind with deduplicated install commands and generated YAML using user values. Each `ANNOTATION_MAPPINGS` entry has an `officialMapping` object with `annotations` (key/transform pairs), `configMap` (key/transform pairs), or `crdKind`/`templateFn` (references `CRD_GENERATORS` object). Transform types: `direct`, `booleanInvert`, `appendRateUnit`, `appendTimeUnit`, `appendBufferSize`, `snippetWrap`, `corsSnippet`, `proxyRedirect`. Collapsible detail cards preserve the original card UI within each step via `buildDetailsToggle()`.
18. **YAML Syntax Highlighting** (v8+): `highlightYaml()` function parses YAML line-by-line using safe DOM methods and applies CSS classes for keys (blue), values (orange), comments (green italic), keywords like `apiVersion`/`kind`/`metadata`/`spec`/`data` (blue bold), booleans (blue), numbers (green), separators (gray), and document separators `---` (gray). No `innerHTML` usage.
19. **Side-by-Side Before/After Comparison** (v8+): Step 1 and Step 2 display `.analyzer-comparison` grid with old (red header) and new (green header) blocks. Annotation lines within each block get diff-style background highlighting via `.yaml-diff-line.removed` (red tint) and `.yaml-diff-line.added` (green tint) using `buildDiffYamlBlock()`. Category comments group annotations when multiple categories are present.
20. **Dark Mode** (v8+): Full dark theme via `body.dark-mode` class with CSS custom property overrides and component-specific dark styles. Toggle button in header with sun/moon SVG icon swap. Preference persisted in `localStorage`. Covers all sections: nav, cards, tables, badges, comparison blocks, YAML blocks, install commands, analyzer UI.
21. **Drag-and-Drop File Upload** (v8+): `#dropZone` wraps the textarea with dragenter/dragleave/drop handlers. Accepts `.yaml`, `.yml`, `.txt` files. Shows overlay with "Drop YAML file here" label during drag. Uses `FileReader` API.
22. **Live Input Status** (v8+): Status bar below textarea shows real-time line count and annotation count (detected via regex). Green/gray dot indicates content presence.
23. **Keyboard Shortcuts** (v8+): Ctrl+Enter (or Cmd+Enter) triggers analysis. Tab key inserts 2 spaces instead of moving focus. Hint text displayed next to action buttons.
24. **Annotation Tooltips** (v8+): `ANNOTATION_TIPS` object provides 50+ short descriptions. Applied as CSS-only tooltips via `data-tip` attribute and `::after` pseudo-element on `.annotation-tooltip` class. Shown on hover for both recognized and unrecognized annotation codes.
25. **Migration Complexity Indicator** (v8+): Success banner includes a dot-based complexity badge: Simple (1 green dot, annotation-only), Moderate (2 yellow dots, includes ConfigMap), Advanced (3 red dots, requires CRDs).
26. **Collapsible Long YAML Blocks** (v8+): YAML output blocks with >12 lines start collapsed (`.collapsed` class) with gradient fade overlay. "Show full YAML (N lines)" / "Collapse" toggle button.
27. **Export Actions** (v8+): "Copy All Migration YAML" button aggregates all steps into a single `---`-separated YAML document. "Download YAML" button generates a `.yaml` file with header comments (URL + date) via `Blob` and `URL.createObjectURL`.
28. **What's Next? Section** (v8+): Contextual links at bottom of results: Migration Checklist, OSS Mappings, Install CRDs (if needed), Official docs. Links use `highlightSection()` for smooth scroll with yellow highlight.
29. **Edit YAML & Re-Analyze Button** (v8+): Pencil-icon button at bottom scrolls back to textarea and focuses it.
30. **Empty State** (v8+): When no results are shown, displays a file-icon SVG with "Paste your Ingress YAML above" message and "Drag & drop a .yaml file, or try Load Sample" hint.
31. **Fade-In Animations** (v8+): Result sections use `@keyframes analyzerFadeIn` with staggered delays (100ms increments) for a smooth reveal effect.
32. **Nav Badge** (v8+): After analysis, the "YAML Analyzer" nav link displays a green badge (`.nav-badge`) showing the total annotation count found.
33. **Active Nav Highlighting** (v8+): `IntersectionObserver` monitors section visibility and toggles `.active` class on corresponding nav links during scroll.
34. **Print Styles** (v8+): `@media print` hides interactive elements (nav, header, footer, buttons, empty state, controls), removes shadows, adjusts YAML block colors to light backgrounds with dark text, expands all collapsed blocks.
35. **Responsive Mobile Layout** (v8+): `@media (max-width: 600px)` stacks analyzer action buttons vertically, makes buttons full-width, adjusts step count badge positioning.
