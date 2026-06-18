---
title: Visual State Coordination
summary: Cross-view interaction is coordinated through globals in main.js, so category changes, selected players, selected teams, and clicked cities are shared by convention rather than encapsulated state.
topics: [runtime, state, interactions, data-visualization]
sources:
  - id: main
    type: file
    path: js/main.js
    note: Defines global visualization instances and interaction state used across classes.
  - id: bar
    type: file
    path: js/barVis.js
    note: Shows selected batsman handling and bar-to-line chart updates.
  - id: line
    type: file
    path: js/lineGraph.js
    note: Shows line chart dependence on global selectedBatsman and selectedCategory.
  - id: treemap
    type: file
    path: js/treemap.js
    note: Shows treemap-to-opponent-bar linkage and default Mumbai Indians selection.
  - id: barmap
    type: file
    path: js/barmap.js
    note: Shows selected-team wrangling and label/color updates.
  - id: map
    type: file
    path: js/mapVis.js
    note: Shows map-to-coin-toss bar coordination through global bar chart instances.
status: active
verified: 2026-06-18
---

The dashboard coordinates interactions through global variables rather than a central store or event bus. `main.js` defines the visualization instances plus `topNum`, `selectedBatsman`, `startColor`, and `selectedCategory` in global scope [@main]. Individual classes read and write those globals directly.

## Batsman State

The batsman views share two globals:

- `selectedCategory`, initialized from `#categorySelector`, chooses one of `sumBatsmanRuns`, `numPlayerOuts`, `average`, or `strikeRate` [@main].
- `selectedBatsman`, initialized to `V Kohli`, tells the line chart which batter to aggregate and tells the bar chart which bar to highlight [@main] [@bar] [@line].

Changing the category through `categoryChange()` resets `selectedBatsman` to a hard-coded representative player for that metric, clears bars, reruns the bar wrangle, and reruns the line wrangle [@main]. Clicking a bar sets `selectedBatsman` to that player and calls `batsmanLineGraph.wrangleData()` directly [@bar]. The bar chart also checks whether the current `selectedBatsman` exists in its dataset and resets it to `null` if not [@bar].

This coupling is the core contract for [[batsman-performance-flow]]. Renaming a metric, changing the category selector values, or moving scripts into modules requires updating `main.js`, `BarVis`, and `lineGraph` together.

## Team State

The team views share state by object reference. `createVis()` constructs `barMapVis` first and passes that instance into `new TreeVis("treevis", data[1], barMapVis)` [@main]. The treemap defaults the opponent bar chart to `Mumbai Indians` during `updateVis()`, and each treemap click calls `barMapVis.updateClickedTeam(d.data.name)` [@treemap].

`updateClickedTeam()` reruns `wrangleData(teamName)`, updates the view, and rewrites the intro label with a colored team name based on a hard-coded color map [@barmap]. The opponent bar chart does not keep a separate selected-team controller; the selected team is the `teamName` argument last passed through this method [@barmap].

## City State

The humidity map calls the two coin-toss bar charts through globals. `createVis()` creates `indiaMapCoinTossDecision` and `indiaMapCoinTossWinner`, then constructs `MapVis` [@main]. The map constructor immediately initializes both toss bars for `Chennai` using a hard-coded humidity of `"88"` [@map]. Clicking a map dot computes that city’s humidity color and calls `coinTossDecision(city, color)` plus `coinTossWinner(city, color)`, which update the global bar chart instances [@map].

This means `MapVis` cannot be reused in isolation unless it is refactored to receive the toss bar instances as constructor arguments. See [[humidity-toss-flow]] for the data behavior behind the interaction.

