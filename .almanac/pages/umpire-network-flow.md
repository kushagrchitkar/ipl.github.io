---
title: Umpire Network Flow
summary: The umpire section builds a force-directed graph from match umpire columns, using node size for individual match counts and link thickness for pair frequency.
topics: [data-visualization, data-model, frontend]
sources:
  - id: index
    type: file
    path: index.html
    note: Defines the umpire narrative, instructions, reset button, and network graph container.
  - id: main
    type: file
    path: js/main.js
    note: Constructs NetworkGraph with the match dataset.
  - id: network
    type: file
    path: js/network.js
    note: Aggregates umpire nodes and pairs, runs the D3 force simulation, and wires reset behavior.
  - id: matches
    type: file
    path: data/matches_cleaned3.csv
    note: Provides Umpire1 and Umpire2 columns for node and link aggregation.
status: active
verified: 2026-06-18
---

The umpire section visualizes individual umpire workload and common umpire pairings. `index.html` gives users explicit instructions to drag nodes apart, hover nodes and links, and use `reset-button` to restore the graph [@index].

`main.js` constructs `new NetworkGraph("network-graph", data[1])`, so the graph uses `matches_cleaned3.csv` [@main]. `NetworkGraph.wrangleData` counts every occurrence of `Umpire1` and `Umpire2` into node counts, then creates unordered umpire-pair keys by sorting the two names and joining them with a hyphen [@network]. The pair counts become links between node indexes [@network].

`NetworkGraph.updateVis` maps individual match counts to circle radius with a square-root scale and pair counts to line thickness with a linear scale [@network]. The highest-count node is filled bright green, and the highest-count link is blue; other links are light blue with opacity proportional to count [@network].

The force simulation uses many-body repulsion, link distance inversely related to pair count, center force, x/y forces, and collision radius based on node size [@network]. Dragging sets `fx` and `fy`, so user-positioned nodes stay fixed until the reset button clears fixed positions and restarts the simulation [@network].

Tooltips are appended to `body` and show either individual matches for a node or matches together for a link [@network]. Labels are drawn only for nodes whose scaled radius is greater than 25, which keeps smaller nodes unlabeled [@network].

## Contracts and Risks

The graph depends on the literal `reset-button` ID existing in the DOM when `updateVis` runs [@index; @network].

Pair keys use a hyphen separator after sorting names [@network]. Existing umpire names in the dataset work with that approach, but names containing hyphens would make `key.split("-")` ambiguous.

The legend labels are hard-coded examples rather than derived from the current data extents [@network]. If the dataset changes substantially, the legend can become misleading even if scales still render.

## Related Pages

Read [[ipl-data-model]] for the match-level umpire columns, [[visualization-runtime]] for chart construction, and [[narrative-page-shell]] for the reset button and network container contract.
