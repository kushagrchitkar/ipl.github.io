---
title: Team Success Views
summary: The team success cluster uses match winners and points-table ranks to connect all-time IPL wins, selected-team opponent records, and year-by-year standings.
topics: [teams, data-visualization, interactions]
sources:
  - id: index
    type: file
    path: index.html
    note: Shows where the team success narrative and containers appear in the fullPage story.
  - id: main
    type: file
    path: js/main.js
    note: Shows construction order and dataset inputs for TreeVis, BarMapVis, and SlopegraphVis.
  - id: treemap
    type: file
    path: js/treemap.js
    note: Shows all-time win aggregation, treemap rendering, team click behavior, and color literals.
  - id: barmap
    type: file
    path: js/barmap.js
    note: Shows selected-team opponent win/loss aggregation and bar updates.
  - id: slope
    type: file
    path: js/slopegraph.js
    note: Shows points-table rank plotting, CSK/MI emphasis, tooltip behavior, and final-season labels.
  - id: matches
    type: file
    path: data/matches_cleaned3.csv
    note: Match-level team and winner source.
  - id: points
    type: file
    path: data/pointstable.csv
    note: Year/rank source for the slope graph.
status: active
verified: 2026-06-18
---

The team success cluster answers two related questions: which IPL teams have won the most matches, and whether the high-win teams also sit high in the points table over time. In the story order, `section4` pairs a treemap of team wins with an opponent bar chart, then later sections introduce Chennai Super Kings versus Mumbai Indians and show a slope graph of team ranks [@index].

## Treemap And Opponent Bars

`TreeVis` receives `data/matches_cleaned3.csv` and counts `WinningTeam` values across all matches [@treemap] [@matches]. It builds a D3 hierarchy with root `Team Wins`, sorts children descending by wins, filters out blank team names, and renders each team as a treemap rectangle sized by wins [@treemap]. Hovering a rectangle temporarily applies hard-coded franchise colors and shows a tooltip with team name and win count [@treemap].

The treemap also controls the adjacent `BarMapVis`. On initial render it calls `barMapVis.updateClickedTeam("Mumbai Indians")`; on click it passes the clicked team name [@treemap]. `BarMapVis` then filters matches where the selected team appears as `Team1` or `Team2`, identifies the opponent, counts wins and losses by opponent, sorts records by wins, and renders horizontal bars showing wins against each opponent [@barmap]. The view calculates losses but only visualizes wins [@barmap].

The team-name color maps are duplicated across `TreeVis`, `BarMapVis`, and `SlopegraphVis`, and spellings are literal. `pointstable.csv` uses `Lucknow Supergiants` and `Pune Warriors India`, while some match-view color branches use `Lucknow Super Giants` and `Pune Warriors` [@points] [@treemap] [@barmap] [@slope]. Future cleanup should centralize franchise identity and aliases if team colors or labels become product-important.

## Slope Graph

`SlopegraphVis` uses `data/pointstable.csv`, not the match table [@main] [@points]. It coerces `Year` and `Rank`, gathers all teams, finds the max year in the dataset, and labels only teams present in that last season [@slope]. Its y-domain is inverted so rank `1` appears at the top [@slope].

The slope graph visually emphasizes Mumbai Indians and Chennai Super Kings by giving them franchise-colored strokes and larger widths; other teams render light grey until hover [@slope]. The surrounding narrative states that CSK and MI performed better than other teams over the years and have declined recently [@index]. Because the slope graph source extends through 2023 while match-level data stops at 2022, agents should avoid assuming every team success view covers the same final season [@matches] [@points].

## Maintenance Notes

`TreeVis.updateVis()` appends rectangles and labels without an update/exit cycle, but the treemap currently renders once, so this is not visible as duplicate marks unless future code reruns `updateVis()` [@treemap]. `SlopegraphVis.updateVis()` also appends paths, labels, and axes imperatively for a one-time render [@slope].

The opponent chart’s y-axis records include opponents sorted by wins, and the x-domain is `max(wins) + 1` [@barmap]. If a selected team has only losses against an opponent, the opponent can still appear with a zero-width bar because records include both wins and losses [@barmap].

