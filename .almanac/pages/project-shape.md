---
title: Project Shape
summary: Pitch Perfect is a static IPL cricket analytics story site whose behavior is assembled by index.html, D3 visualization classes, local datasets, remote libraries, and video-backed fullPage sections.
topics: [project-shape, static-site, data-visualization]
sources:
  - id: readme
    type: file
    path: README.md
    note: Describes the repository as an alternate maintained version of the Pitch Perfect course project site and records performance/readability concerns.
  - id: index
    type: file
    path: index.html
    note: Defines the narrative sections, CDN dependencies, visualization containers, script load order, and fullPage initialization.
  - id: main
    type: file
    path: js/main.js
    note: Loads project data and constructs every visualization class.
  - id: style
    type: file
    path: css/style.css
    note: Defines the full-screen section layout, video layering, chart container sizes, typography, and responsive constraints currently in use.
status: active
verified: 2026-06-18
---

Pitch Perfect is a static, browser-only data story about the Indian Premier League. The repository has no package manifest, build step, local application server, or test harness; `index.html` loads CDN libraries, local CSS, local data files, local visualization classes, and `js/main.js` directly in the browser [@index]. The README frames this repository as an alternate maintained copy of the official course project site, with ongoing optimization focused on compressed video backgrounds, laptop-oriented CSS, and future smartphone font/readability work [@readme].

The site is organized as a scroll-driven story. `index.html` creates a `#fullPage` container with full-screen sections for cricket context, IPL context, team success, CSK versus MI, batsman analysis, umpire analysis, humidity/toss analysis, and the closing screen [@index]. fullPage.js supplies automatic section scrolling and right-side navigation; the first section delays the title overlay for 4.6 seconds after it loads [@index]. The visual analysis pages are not routes. They are DOM containers inside these sections.

`js/main.js` is the runtime entrypoint. It loads six datasets with D3, normalizes selected numeric/date fields, then constructs all visualization objects once the Promise resolves [@main]. The visualization classes are ordered by dependency: the opponent bar chart is created before the treemap because the treemap receives the bar chart instance and calls it when a team is clicked [@main].

The project’s durable shape is:

| Layer | Project role |
| --- | --- |
| `index.html` | Narrative order, visualization mount points, CDN scripts, fullPage setup. |
| `css/style.css` | Global typography, chart dimensions, video backgrounds, absolute overlay positions. |
| `js/main.js` | Data loading, data coercion, global interaction state, object construction. |
| `js/*.js` visualization classes | D3 rendering and interaction logic for individual views. |
| `data/*` | Precomputed IPL match, ball-by-ball, batsman, points table, and humidity datasets. |
| `img/*` | Video/image assets that carry the first-viewport and transition sections. |

Future agents should treat [[dashboard-runtime]] and [[data-contracts]] as the highest-value technical pages after this one. The story-level modules are [[team-success-views]], [[batsman-performance-flow]], [[umpire-network]], and [[humidity-toss-flow]].

## Constraints

The code assumes a browser runtime with network access to CDN libraries and the remote India state GeoJSON. Because local data is fetched with `d3.csv` and `d3.json`, opening `index.html` as a plain file may be blocked by browser CORS rules; serving the directory over HTTP is the practical way to exercise the dashboard [@index] [@main].

Most dimensions are measured from already-laid-out DOM containers at visualization construction time. If a container has zero or unexpected dimensions when its class initializes, its SVG will inherit that bad size [@main] [@style]. This matters because fullPage sections, Bootstrap columns, and fixed viewport-height chart containers all participate in layout before D3 reads widths and heights.

