---
title: Umpire Network
summary: The umpire network derives individual umpire match counts and pair frequencies from match-level Umpire1/Umpire2 fields, then renders them as a force-directed graph with drag and reset behavior.
topics: [umpires, data-visualization, interactions]
sources:
  - id: index
    type: file
    path: index.html
    note: Defines the umpire narrative, instructions, reset button, and network container.
  - id: main
    type: file
    path: js/main.js
    note: Shows NetworkGraph construction from the match dataset.
  - id: network
    type: file
    path: js/network.js
    note: Shows umpire count/pair derivation, force simulation, tooltips, drag behavior, reset behavior, and legend.
  - id: matches
    type: file
    path: data/matches_cleaned3.csv
    note: Match-level source for Umpire1 and Umpire2.
status: active
verified: 2026-06-18
---

The umpire section turns match officiating fields into a network view. `index.html` introduces the section with instructions to drag nodes apart, hover nodes for individual match counts, hover links for joint match counts, and use `#reset-button` to release fixed node positions [@index]. `main.js` constructs `new NetworkGraph("network-graph", data[1])`, so the only data source is `data/matches_cleaned3.csv` [@main] [@matches].

`NetworkGraph.wrangleData()` counts each appearance in `Umpire1` and `Umpire2` into an `umpireCounts` object, converts that to nodes `{ id, count }`, then creates an order-insensitive pair key by sorting `[Umpire1, Umpire2]` and joining with `-` [@network]. Pair frequencies become links with numeric `source` and `target` indexes into the node array and `value` equal to matches officiated together [@network].

The renderer scales node radius by individual count and link thickness/distance/transparency by pair count [@network]. The largest individual count gets a distinct green fill, and the thickest pair link gets a distinct blue stroke [@network]. Labels render only for nodes whose scaled radius is greater than 25, which keeps low-count umpires unlabeled until hover [@network].

## Interaction Contract

The force simulation uses charge, link, center, X/Y, and collision forces [@network]. Dragging a node sets `fx` and `fy`, so dragged nodes stay fixed after release. The reset button sets every node’s `fx` and `fy` back to `null` and restarts the simulation [@network].

The reset behavior depends on the exact DOM id `reset-button` existing before `NetworkGraph.updateVis()` runs [@index] [@network]. If the network is moved into a different component or loaded conditionally, the reset listener should be moved out of the visualization class or guarded.

## Data Assumptions

The graph assumes every match row has usable `Umpire1` and `Umpire2` strings [@network]. It does not filter blank or missing umpire names before counting. If future data includes absent officials, those values will become nodes and pair endpoints unless wrangling adds validation.

The pair key joins names with `-`; this is safe for current data as used, but it is a string convention rather than a structured tuple [@network]. A future parser should avoid splitting ambiguity if official names containing hyphens become relevant.

