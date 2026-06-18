---
title: Batsman Performance Flow
summary: The batsman section pairs a top-N metric bar chart with a clicked-player yearly line chart through shared selectedCategory, selectedBatsman, and topNum globals.
topics: [data-visualization, data-model, frontend]
sources:
  - id: index
    type: file
    path: index.html
    note: Defines the batsman metric selector, slider, bar chart container, and line chart container.
  - id: main
    type: file
    path: js/main.js
    note: Defines selectedCategory, selectedBatsman, topNum, categoryChange, chart construction, and slider wiring.
  - id: barvis
    type: file
    path: js/barVis.js
    note: Renders the top-N batsman bars and updates selectedBatsman on click.
  - id: linegraph
    type: file
    path: js/lineGraph.js
    note: Joins ball and match data to aggregate one batsman's yearly metrics.
  - id: batsman
    type: file
    path: data/batsmanData.json
    note: Provides precomputed batsman metrics for the bar chart.
  - id: ball
    type: file
    path: data/IPL_Ball_by_Ball_2008_2022_cleaned.csv
    note: Provides delivery-level data for yearly line aggregation.
  - id: matches
    type: file
    path: data/matches_cleaned3.csv
    note: Provides match dates for the ball-to-year join.
status: active
verified: 2026-06-18
---

The batsman section lets users choose a metric, choose how many top players to show, and click a bar to see that player's yearly trend. The controls live in `index.html`: `categorySelector` calls `categoryChange()` inline, `slider` is turned into a noUiSlider, `barDiv` hosts `BarVis`, and `lineDiv` hosts `lineGraph` [@index].

The bar chart and line chart intentionally use different data grains. `BarVis` reads the precomputed `batsmanData.json` summary table, sorts by the selected metric, slices to `topNum`, and renders the current top-N [@barvis]. `lineGraph` reads delivery-level ball data plus match data, joins each delivery to its match by `ID`, filters to `selectedBatsman`, groups by match year, and computes the selected metric per year [@linegraph].

The active global state is defined in `main.js`: `topNum` starts at 10, `selectedBatsman` starts as V Kohli, `selectedCategory` is read from the DOM selector, and `startColor` is `#6F989D` [@main]. The slider updates `topNum`, changes the visible label, and calls `batsmanBarGraph.wrangleData()`; it does not directly redraw the line chart [@main].

`categoryChange` changes both metric and default selected player. It maps runs to V Kohli, outs to RG Sharma, average to KL Rahul, and strike rate to AD Russell, then clears bars, redraws the bar chart, and redraws the line chart [@main]. Bar clicks update `selectedBatsman`, recolor the clicked bar, and call `batsmanLineGraph.wrangleData` [@barvis].

Metric definitions differ slightly between the precomputed bar data and the line chart code. The bar data stores `strikeRate` as whole-number values such as `126` for V Kohli [@batsman]. The line chart computes strike rate as `sum(batsman_run) / group.length * 100`, where `group.length` is the number of delivery rows for that batter in that year [@linegraph].

## Maintenance Implications

Changing the category option values requires updating all metric maps together: the `<option value>` attributes in `index.html`, `selectedCategory` consumers in `main.js`, labels in `barVis.js` and `lineGraph.js`, and the field names in `batsmanData.json` [@index; @main; @barvis; @linegraph].

The line chart performs a nested `find` over match data for each ball row whenever it wrangles data [@linegraph]. With the current 225,954 ball rows this is acceptable for a static course project, but a future larger dataset should pre-index matches by `ID`.

`BarVis.organizeDataInitial` is a retained generation helper, not the active path. It describes how `batsmanData.json` was derived and logs JSON for manual copying, but `main.js` currently loads the JSON file directly [@barvis; @main].

## Related Pages

Read [[ipl-data-model]] for the ball and batsman-summary grains, [[visualization-runtime]] for shared globals, and [[narrative-page-shell]] for the selector, slider, and chart containers.
