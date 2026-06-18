---
title: Batsman Performance Flow
summary: The batsman flow combines a precomputed player ranking JSON, ball-by-ball yearly aggregation, a category dropdown, and a top-N slider through shared global state.
topics: [batsmen, data-visualization, interactions, data-contracts]
sources:
  - id: index
    type: file
    path: index.html
    note: Defines the batsman story section, category dropdown, slider container, bar chart container, and line chart container.
  - id: main
    type: file
    path: js/main.js
    note: Defines selectedCategory, selectedBatsman, categoryChange, slider setup, and construction of BarVis and lineGraph.
  - id: bar
    type: file
    path: js/barVis.js
    note: Shows batsman ranking, top-N slicing, category-specific bars, tooltips, and bar click behavior.
  - id: line
    type: file
    path: js/lineGraph.js
    note: Shows match/ball join, selected-batsman filtering, year aggregation, and line/dot rendering.
  - id: balls
    type: file
    path: data/IPL_Ball_by_Ball_2008_2022_cleaned.csv
    note: Ball-by-ball source for yearly batsman aggregation.
  - id: matches
    type: file
    path: data/matches_cleaned3.csv
    note: Match date source joined into ball rows by ID.
  - id: batsman
    type: file
    path: data/batsmanData.json
    note: Precomputed ranking data used by the bar chart.
status: active
verified: 2026-06-18
---

The batsman flow lets a reader rank IPL batsmen by one of four metrics and then inspect the selected player’s yearly performance. The story section provides a dropdown for `Batsman Runs`, `Player Outs`, `Average`, and `Strike Rate`, a noUiSlider top-N control, a ranking bar chart in `#barDiv`, and a yearly line chart in `#lineDiv` [@index].

## Ranking Bars

`BarVis` consumes `data/batsmanData.json`, a precomputed 136-row player summary with `name`, `sumBatsmanRuns`, `numPlayerOuts`, `average`, and `strikeRate` [@batsman]. On each wrangle, it copies the data, filters out zero averages when `selectedCategory === "average"`, sorts descending by the selected metric, slices to global `topNum`, and updates bars, labels, axis titles, and tooltips [@bar].

The top-N slider is created in `main.js` with range `3` to `20`, step `1`, and initial value `10`. Each slider update sets global `topNum`, updates the visible label, and reruns `batsmanBarGraph.wrangleData()` [@main]. The slider does not directly update the line chart; the line chart changes only when `selectedBatsman` or `selectedCategory` changes [@main].

Clicking a bar sets global `selectedBatsman`, highlights that bar, and calls `batsmanLineGraph.wrangleData()` [@bar]. Changing the category calls `categoryChange()`, which maps each metric to a representative default player: `V Kohli` for runs, `RG Sharma` for outs, `KL Rahul` for average, and `AD Russell` for strike rate [@main].

## Yearly Line Chart

`lineGraph` receives the full ball-by-ball dataset plus the match table [@main]. For every wrangle it maps each ball row to a combined object by finding the match with the same `ID`, filters combined rows to global `selectedBatsman`, and groups the result by `Date.getFullYear()` [@line]. It calculates yearly runs, wicket deliveries, average, and strike rate, converts years back into parsed dates for the x-scale, and renders either a line or a dot if the selected batsman has only one year of data [@line].

The join is simple and expensive: `ballData.map()` calls `matchData.find()` for every ball row [@line]. With 225,954 ball rows and 950 match rows, this is acceptable for a course-scale static site but should be indexed by `ID` if performance becomes a maintenance goal [@balls] [@matches].

## Metric Semantics

The bar chart and line chart do not compute every metric the same way. The precomputed JSON’s `strikeRate` values are loaded as-is, while the line chart calculates `strikeRate` as runs divided by balls in the yearly group, multiplied by 100 [@batsman] [@line]. `BarVis.organizeDataInitial()`, now unused at runtime, shows a previous derivation where `strikeRate` was stored as runs divided by total occurrences without multiplying by 100 [@bar]. Future work should normalize this before comparing bar values directly to line values.

`lineGraph` uses `isWicketDelivery` as `numPlayerOuts`, not `player_out === selectedBatsman` [@line]. That means yearly “outs” can count wicket deliveries in rows where the selected batter may not be the dismissed player. The precomputed JSON derivation in `BarVis.organizeDataInitial()` counted dismissals where `entry.player_out === batterName` [@bar]. This is a real metric-contract difference to resolve before presenting outs/average as statistically strict.

