---
title: Visualization Runtime
summary: main.js loads the datasets, normalizes a few fields, creates global chart instances, and coordinates cross-chart interactions through shared globals.
topics: [frontend, data-visualization, data-model]
sources:
  - id: main
    type: file
    path: js/main.js
    note: Shows dataset loading, data coercion, global state, chart construction, category changes, and slider wiring.
  - id: index
    type: file
    path: index.html
    note: Provides the DOM containers and script order required by the runtime.
  - id: barvis
    type: file
    path: js/barVis.js
    note: Uses selectedBatsman, selectedCategory, topNum, and batsmanLineGraph globals.
  - id: linegraph
    type: file
    path: js/lineGraph.js
    note: Uses selectedBatsman and selectedCategory globals to redraw the selected batsman's yearly line chart.
  - id: mapvis
    type: file
    path: js/mapVis.js
    note: Uses global toss bar graph instances when map city points are clicked.
status: active
verified: 2026-06-18
---

`js/main.js` is the runtime coordinator for the static page. It loads the project datasets with `Promise.all`, calls `cleanData`, constructs every visualization object, and creates the noUiSlider control for batsman top-N selection [@main]. The code assumes the DOM and local chart classes already exist because `index.html` loads `main.js` after the visualization scripts and after the chart container markup [@index].

The data load order is positional:

| Index | Source | Main consumer |
| --- | --- | --- |
| `data[0]` | `data/IPL_Ball_by_Ball_2008_2022_cleaned.csv` | [[batsman-performance-flow]] line chart |
| `data[1]` | `data/matches_cleaned3.csv` | team success, umpire network, map city match filters |
| `data[2]` | `data/batsmanData.json` | batsman bar chart |
| `data[3]` | `data/pointstable.csv` | slope graph |
| `data[4]` | remote India GeoJSON | humidity map geography |
| `data[5]` | `data/matches_humidity.json` | humidity map and toss bars |

`cleanData` performs only narrow coercion. It converts selected ball-by-ball fields to numbers, parses `matches_cleaned3.csv` dates using `%Y-%m-%d`, and converts match `Season` and `Margin` to numbers [@main]. It does not normalize team names, city aliases, blank winners, or humidity numeric strings; chart modules handle or expose those values locally.

Cross-chart communication relies on globals instead of events or imports. `selectedCategory`, `selectedBatsman`, `topNum`, and `startColor` live in `main.js`; `BarVis` reads `selectedCategory` and `topNum`, writes `selectedBatsman` when a bar is clicked, and calls `batsmanLineGraph.wrangleData` [@main; @barvis]. `lineGraph` reads the same `selectedBatsman` and `selectedCategory` to recompute yearly aggregates [@linegraph]. `MapVis` calls `indiaMapCoinTossDecision.wrangleData` and `indiaMapCoinTossWinner.wrangleData` through globals instead of receiving those chart objects as constructor arguments [@mapvis].

`categoryChange` hard-codes the default selected batsman for each metric: V Kohli for runs, RG Sharma for outs, KL Rahul for average, and AD Russell for strike rate [@main]. It clears the bar chart, recomputes the top-N bars, and redraws the line chart. This function is invoked directly from the `onchange` attribute on the category `<select>` in `index.html` [@index].

The runtime also declares a `citiesCSV` list and `loadCitiesCSV` helper for `data/IndiaWeather/`, but the active `promises` array does not call that helper. The current app uses the precomputed `matches_humidity.json` file instead [@main]. That historical merge path is documented in [[humidity-toss-flow]].

## Maintenance Implications

Adding a chart means adding the DOM container in [[narrative-page-shell]], loading the script before `main.js`, adding the dataset to the positional `Promise.all` array if needed, and updating `createVis` [@main; @index].

Renaming a chart container ID is a behavioral change. Every chart constructor measures its parent element by ID before creating the SVG, so missing or hidden containers produce bad dimensions or runtime failures [@main].
