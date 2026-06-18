---
title: Getting Started
summary: Start here to navigate the Pitch Perfect wiki by runtime architecture, data contracts, interaction flows, and presentation constraints.
topics: [navigation, project-shape]
sources:
  - id: project-shape
    type: file
    path: .almanac/pages/project-shape.md
    note: Local synthesis page for the project structure.
  - id: runtime
    type: file
    path: .almanac/pages/dashboard-runtime.md
    note: Local synthesis page for browser load order and dependencies.
  - id: data
    type: file
    path: .almanac/pages/data-contracts.md
    note: Local synthesis page for dataset contracts.
status: active
verified: 2026-06-18
---

This wiki is the front door for future work on Pitch Perfect, a static IPL analytics story site. Start with [[project-shape]] to understand the repository shape, the browser-only architecture, and the four major visualization clusters. Then read [[dashboard-runtime]] for load order, CDN dependencies, global constructors, and why script order matters.

Read [[data-contracts]] before changing any visualization. Most behavior is controlled by precomputed CSV/JSON files, exact field names, and dataset date ranges. The match table covers 2008-2022, the ball-by-ball table covers 2008-2022, and the points table extends to 2023, so not every chart has the same temporal boundary.

For feature work, choose the relevant cluster:

- [[team-success-views]] for the treemap, selected-team opponent bars, and CSK/MI rank slope graph.
- [[batsman-performance-flow]] for the category dropdown, top-N slider, ranking bars, and selected-batsman yearly line chart.
- [[umpire-network]] for individual umpire counts, pair frequencies, force layout, drag behavior, and reset behavior.
- [[humidity-toss-flow]] for the India humidity map, city clicks, toss decision counts, and toss-winner outcome bars.

For cross-cutting behavior, read [[visual-state-coordination]]. The dashboard relies on globals in `js/main.js` and direct references between visualization objects; interactions are coordinated by convention rather than by modules, events, or a state store.

For visual and operational work, read [[presentation-assets-and-layout]] and [[static-site-operations]]. The site is media-heavy, layout-sensitive, and verified in a browser rather than by a build or test command.

