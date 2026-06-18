---
title: IPL Data Model
summary: The app uses five local IPL datasets with distinct grains: ball deliveries, matches, precomputed batsman totals, yearly points-table ranks, and match records enriched with humidity.
topics: [data-model, data-visualization]
sources:
  - id: ball
    type: file
    path: data/IPL_Ball_by_Ball_2008_2022_cleaned.csv
    note: Ball-by-ball delivery data used for yearly batsman aggregation.
  - id: matches
    type: file
    path: data/matches_cleaned3.csv
    note: Match-level IPL data used by team, umpire, and map flows.
  - id: batsman
    type: file
    path: data/batsmanData.json
    note: Precomputed batsman summary metrics used by the top-N bar chart.
  - id: points
    type: file
    path: data/pointstable.csv
    note: Yearly standings data used by the slope graph.
  - id: humidity
    type: file
    path: data/matches_humidity.json
    note: Match data enriched with humidity and geolocation fields for the map and toss charts.
  - id: main
    type: file
    path: js/main.js
    note: Shows which fields are coerced before chart construction.
  - id: barvis
    type: file
    path: js/barVis.js
    note: Shows the retained browser-side aggregation helper that produced the batsman summary JSON.
status: active
verified: 2026-06-18
---

The project's chart data is local and denormalized for browser use. The five local datasets have different grains and are joined only inside visualization code, not through a central model layer. `js/main.js` loads all of them up front before constructing charts [@main].

`data/matches_cleaned3.csv` is the match-level anchor for most flows. It has 950 rows covering IPL seasons 2008 through 2022, with fields for city, date, teams, venue, toss winner and decision, winning team, margin, player of match, player lists, umpires, and cleaned venue name [@matches]. Team success charts count `WinningTeam`, compare `Team1` and `Team2`, and use `Umpire1`/`Umpire2` for the network [@matches].

`data/IPL_Ball_by_Ball_2008_2022_cleaned.csv` is delivery-level data. It has 225,954 rows, 950 match IDs, and 605 distinct batters in the current file. The active line chart uses `ID`, `batter`, `batsman_run`, and `isWicketDelivery`, then joins each delivery to match data by `ID` to recover the match year [@ball].

`data/batsmanData.json` is a precomputed batsman summary table with 136 records and fields `name`, `sumBatsmanRuns`, `numPlayerOuts`, `average`, and `strikeRate` [@batsman]. `BarVis.organizeDataInitial` still contains the original browser-side aggregation code and a comment saying the generated JSON was copied from the console, but the active path loads the JSON directly [@barvis; @main].

`data/pointstable.csv` is yearly standings data from 2008 through 2023. It includes rank, wins, losses, no-results, net run rate, runs for/against, and points [@points]. This range extends one season beyond the match and ball-by-ball datasets, so standings views can mention 2023 while match-derived views stop at 2022.

`data/matches_humidity.json` repeats match-level fields and adds `humidity`, `latitude`, `longitude`, and `elevation` for humidity/toss visualizations [@humidity]. It has 950 rows, matching `matches_cleaned3.csv`, and includes cities outside India such as Dubai, Sharjah, Abu Dhabi, Cape Town, and Centurion [@humidity]. The map flow filters out UAE cities at draw time because the GeoJSON is India-only [@humidity].

## Important Data Contracts

Match IDs are numeric after `cleanData` runs on the CSV datasets. The ball-to-match join in `lineGraph` depends on `ball.ID === match.ID`, so adding data without numeric coercion breaks yearly batsman aggregation [@main].

Match `Date` is parsed only in `matches_cleaned3.csv`, not in `matches_humidity.json` [@main]. `MapVis.mergeData` is historical code that expects `match.Date` to be parseable, but the active map path uses the already enriched humidity JSON [@main].

Team names are not canonicalized across files. Examples include `Lucknow Super Giants` in match-derived code and `Lucknow Supergiants` in the points table, and `Pune Warriors` versus `Pune Warriors India` in color mappings [@matches; @points]. Future cross-file team joins need explicit alias handling.

Blank or placeholder values remain in the datasets. `TreeVis` filters out a blank winning-team entry before drawing, and `MapVis` filters points with non-numeric humidity [@matches; @humidity].

## Related Pages

Read [[visualization-runtime]] for load order and coercion, [[team-success-flow]] for match and points-table consumers, [[batsman-performance-flow]] for ball and batsman-summary consumers, and [[humidity-toss-flow]] for the enriched humidity file.
