---
title: Team Success Flow
summary: The team-success section combines a treemap of total match wins, a clicked-team opponent bar chart, and a points-table slope graph for MI/CSK rank context.
topics: [data-visualization, data-model]
sources:
  - id: index
    type: file
    path: index.html
    note: Defines the team-success narrative sections and chart containers.
  - id: main
    type: file
    path: js/main.js
    note: Constructs BarMapVis, TreeVis, and SlopegraphVis with their datasets.
  - id: treemap
    type: file
    path: js/treemap.js
    note: Aggregates WinningTeam counts and sends clicked teams to BarMapVis.
  - id: barmap
    type: file
    path: js/barmap.js
    note: Aggregates selected-team wins and losses against opponents.
  - id: slope
    type: file
    path: js/slopegraph.js
    note: Draws yearly team rank paths from the points-table dataset.
  - id: matches
    type: file
    path: data/matches_cleaned3.csv
    note: Provides match-level winners and teams for the treemap and opponent bar chart.
  - id: points
    type: file
    path: data/pointstable.csv
    note: Provides rank-by-year data for the slope graph.
status: active
verified: 2026-06-18
---

The team-success narrative asks which IPL teams have won the most and then narrows to Chennai Super Kings and Mumbai Indians. `index.html` places a treemap and opponent bar chart side by side, then follows with a CSK-vs-MI video slide and a slope graph section about points-table rank over time [@index].

`main.js` creates `barMapVis = new BarMapVis("barvis", data[1])`, then `treemapVis = new TreeVis("treevis", data[1], barMapVis)` [@main]. That constructor order matters: the treemap holds a reference to the bar chart so a team click can update the opponent chart [@treemap].

`TreeVis` groups `matches_cleaned3.csv` by `WinningTeam`, counts wins, sorts descending, filters out the blank team name, and renders a D3 treemap [@treemap]. On initial render it calls `barMapVis.updateClickedTeam("Mumbai Indians")`, so Mumbai Indians is the default selected team even before user interaction [@treemap]. Clicking a treemap rectangle calls `updateClickedTeam` with the clicked team name [@treemap].

`BarMapVis` receives the same match-level dataset. For the selected team, it scans matches where that team appears as `Team1` or `Team2`, identifies the opponent, increments wins when `WinningTeam` equals the selected team, otherwise increments losses, and sorts opponent records by wins [@barmap]. The rendered bars show only wins, while the internal `records` objects also keep losses [@barmap].

`SlopegraphVis` uses `pointstable.csv`, not match rows. It coerces `Year` and `Rank`, finds all teams, draws one path per team with rank on the vertical axis and year on the horizontal axis, and labels teams present in the latest points-table season [@slope]. Mumbai Indians and Chennai Super Kings receive thicker colored lines by default; other teams are grey until hover [@slope].

## Risk Points

Team color mappings are duplicated in `treemap.js`, `barmap.js`, and `slopegraph.js` [@treemap; @barmap; @slope]. Adding or renaming a team can produce inconsistent colors unless all mappings are updated.

The opponent bar chart treats any match involving the selected team that it did not win as a loss [@barmap]. It does not separately model no-result, abandoned, or blank winner states.

The treemap and opponent bar chart stop at 2022 because `matches_cleaned3.csv` stops there, while the slope graph extends to 2023 because `pointstable.csv` includes 2023 [@matches; @points].

## Related Pages

Read [[ipl-data-model]] for dataset ranges and team-name caveats, [[visualization-runtime]] for constructor order, and [[narrative-page-shell]] for the section containers that host these charts.
