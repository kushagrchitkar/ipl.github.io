---
title: Static Site Operations
summary: Operating this repository means serving static files over HTTP, preserving CDN/network dependencies, and checking browser-rendered chart behavior because there is no local build or test command.
topics: [operations, static-site, verification]
sources:
  - id: readme
    type: file
    path: README.md
    note: Provides the public GitHub Pages link and maintenance intent.
  - id: index
    type: file
    path: index.html
    note: Shows direct asset paths and CDN dependencies.
  - id: main
    type: file
    path: js/main.js
    note: Shows d3.csv/d3.json file loading and the remote GeoJSON request.
  - id: gitignore
    type: file
    path: .gitignore
    note: Shows Almanac runtime files are ignored while wiki sources are not.
status: active
verified: 2026-06-18
---

This repository operates as a static website. The README points to a GitHub Pages deployment at `https://kushagrchitkar.github.io/ipl.github.io/` [@readme]. Locally, the practical verification path is to serve the repo root with a simple HTTP server and load `index.html`; D3 fetches local CSV/JSON assets with `d3.csv` and `d3.json`, which should not be assumed to work from a `file://` URL in every browser [@index] [@main].

There is no `package.json`, build script, formatter configuration, or automated test command in the current repo. Verification is therefore browser-oriented:

- Confirm all CDN dependencies load: Bootstrap, noUiSlider, fullPage.js, Google Fonts, and D3 v7 [@index].
- Confirm the remote India state GeoJSON loads; if it fails, `Promise.all` rejects and `createVis()` does not run [@main].
- Exercise fullPage navigation and video-backed sections.
- Check the treemap click updates the opponent bar chart.
- Check batsman category changes, slider changes, bar clicks, and line chart updates.
- Check the umpire network hover, drag, and reset button.
- Check map city hover/click behavior and both toss bar charts.

The repository’s `.gitignore` ignores Almanac runtime database/job files but not wiki source files [@gitignore]. Wiki source changes under `.almanac/pages/` are intended to be reviewable and committable project artifacts, while `.almanac/jobs/` and `.almanac/index.db*` remain runtime outputs [@gitignore].

## Dependency Fragility

The runtime uses public CDN URLs with integrity attributes for some dependencies [@index]. If a CDN becomes unavailable, the local repository has no vendored fallback. The map has an additional non-CDN dependency on a GitHub-hosted GeoJSON URL [@main]. These dependencies are acceptable for a course/project site but matter for demos, archived builds, and offline work.

## Data Regeneration

The code contains historical derivation snippets for `batsmanData.json` and `matches_humidity.json`, but they are not executable repo operations. `BarVis.organizeDataInitial()` logs generated batsman JSON, and `MapVis.mergeData()` logs merged humidity JSON using a missing `data/IndiaWeather/` source folder [@main]. Agents changing core datasets should document how derived files were regenerated or add a repeatable script.

