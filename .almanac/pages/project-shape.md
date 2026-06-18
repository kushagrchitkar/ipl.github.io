---
title: Project Shape
summary: This project is a static IPL data-story website whose behavior is concentrated in index.html, global JavaScript classes, local datasets, and hosted client libraries.
topics: [frontend, data-visualization, project-memory]
sources:
  - id: readme
    type: file
    path: README.md
    note: States that this repository is an alternate maintained version of the original course project site and records current optimization goals.
  - id: index
    type: file
    path: index.html
    note: Defines the scroll narrative, DOM containers, CDN dependencies, media backgrounds, and script-loading order.
  - id: main
    type: file
    path: js/main.js
    note: Loads all chart datasets and constructs the visualization objects.
status: active
verified: 2026-06-18
---

This repository is an alternate maintained version of the `pitch-perfect.github.io` course project website. The public site is an IPL-focused data story called "Pitch Perfect"; the README frames this repo as the place where post-submission performance and usability improvements can continue because the original course project had deadline restrictions [@readme].

The application is a static browser page. There is no package manifest, bundler, local build step, test runner, backend, or module system in the current repo. `index.html` loads third-party CSS and scripts from CDNs, lays out every full-page section, and then loads local visualization classes followed by `js/main.js` [@index]. A future agent should assume changes are deployed as static file changes unless a new toolchain is intentionally introduced.

The central project boundary is not file ownership; it is the narrative page. Sections in `index.html` create a linear cricket analytics story: cricket and IPL context, team wins, MI/CSK ranks, batsman statistics, umpire pairings, humidity and toss decisions, then the closing slide [@index]. The local JavaScript modules are chart classes that attach to IDs already present in that story shell, so markup IDs are part of the runtime contract.

`js/main.js` is the orchestration point. It loads five local datasets plus one remote India GeoJSON, coerces selected CSV fields, initializes global chart instances, and creates the batsman top-N slider [@main]. The classes themselves are not imported modules; they become globals because `index.html` includes each script before `main.js` [@index].

Current maintenance goals are product-facing rather than architectural. The README calls out compressed video backgrounds, CSS tailored to MacBook/Linux laptop displays, and future work for broader CSS compatibility and smartphone-readable fonts [@readme]. Those goals connect directly to [[media-and-responsive-constraints]] and to the fact that the page relies on heavy full-screen video sections rather than a lightweight static article.

## Read Next

Read [[narrative-page-shell]] for the markup and dependency-loading contract, [[visualization-runtime]] for chart initialization and global state, and [[ipl-data-model]] before changing any chart logic. The major chart clusters are [[team-success-flow]], [[batsman-performance-flow]], [[umpire-network-flow]], and [[humidity-toss-flow]].
