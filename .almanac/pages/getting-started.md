---
title: Getting Started
summary: Start here to navigate the Pitch Perfect wiki by project shape, runtime contracts, data model, visualization flows, and maintenance risks.
topics: [project-memory]
sources:
  - id: project-shape
    type: file
    path: .almanac/pages/project-shape.md
    note: Provides the high-level repository model used by this navigation page.
  - id: runtime
    type: file
    path: .almanac/pages/visualization-runtime.md
    note: Provides the runtime and global-state model used by this navigation page.
status: active
verified: 2026-06-18
---

This wiki is the front door for future work on Pitch Perfect, a static IPL data-story website. Start with [[project-shape]] to understand why the repo is organized around one narrative page, local datasets, CDN browser libraries, and global visualization classes.

For code changes, read [[narrative-page-shell]] and [[visualization-runtime]] together. The first explains why `index.html` section order, element IDs, video layers, and script order are runtime contracts. The second explains how `js/main.js` loads datasets, coerces data, creates chart objects, and coordinates interactions through globals.

Before changing chart logic or replacing data, read [[ipl-data-model]]. It explains the grains and ranges of the five local datasets, where current views stop at 2022 versus 2023, and where team/city names are not normalized.

For visualization-specific work, use these routes:

- [[team-success-flow]] for the treemap, selected-team opponent bars, and MI/CSK slope graph.
- [[batsman-performance-flow]] for the metric selector, top-N slider, batsman bar chart, and clicked-player yearly line chart.
- [[umpire-network-flow]] for the force-directed umpire pairing graph and reset behavior.
- [[humidity-toss-flow]] for the India humidity map, remote GeoJSON dependency, and toss-decision bar charts.

For presentation, dependency, or deployment changes, read [[frontend-dependencies]] and [[media-and-responsive-constraints]]. They capture the current static-CDN model, fullPage/noUiSlider/D3 assumptions, full-screen video cost, absolute overlay positioning, and README-documented responsive goals.

The highest-risk project invariant is that markup IDs, global JavaScript names, and dataset field names are coupled. A small rename can affect multiple pages because there is no bundler, type checker, or automated test suite enforcing those contracts.
