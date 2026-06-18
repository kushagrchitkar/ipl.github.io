---
title: Narrative Page Shell
summary: index.html is both the story script and the runtime loader, so section order, element IDs, media tags, and script order are chart contracts.
topics: [frontend, data-visualization]
sources:
  - id: index
    type: file
    path: index.html
    note: Defines all fullPage sections, chart containers, CDN dependencies, and local script order.
  - id: style
    type: file
    path: css/style.css
    note: Defines full-screen video layers, text boxes, chart container dimensions, and responsive-sensitive positioning.
status: active
verified: 2026-06-18
---

`index.html` is the canonical story shell for Pitch Perfect. It does more than host charts: it defines the fullPage.js section sequence, embeds the video backgrounds, names every visualization container, and loads every dependency required by the global JavaScript classes [@index].

The page is organized as one `#fullPage` element containing full-screen `.section` blocks. Narrative video sections use `.myVideo` plus absolutely positioned `.layer` overlays; analytic sections expose chart container IDs such as `treevis`, `barvis`, `slopegraph`, `barDiv`, `lineDiv`, `network-graph`, `mapDiv`, `coin-toss-decision-bar`, and `coin-toss-winner-bar` [@index]. These IDs are not presentation details. The chart constructors in [[visualization-runtime]] query them directly to measure dimensions and append SVGs.

The script-loading order is a runtime contract. D3, fullPage.js, Bootstrap JS, and noUiSlider are loaded from CDNs before local visualization scripts. Local classes are then loaded in plain script tags, and `js/main.js` is loaded last so it can instantiate `BarMapVis`, `TreeVis`, `SlopegraphVis`, `NetworkGraph`, `BarVis`, `lineGraph`, `CoinTossBarGraph`, and `MapVis` as globals [@index].

The page initializes fullPage.js on `DOMContentLoaded` with `autoScrolling`, right-side navigation, and an `afterLoad` hook that reveals the opening title overlay after a delay when the first section is reached [@index]. The navigation dots are visually replaced by `img/cricketball.png` through CSS targeting `.fp-right ul li a span` [@style].

CSS is tightly coupled to section numbers. Rules such as `#section5 .layer2`, `#section7 .layer1`, `#section11 .layer`, and `#section13 .layer2` use absolute offsets, z-indexes, and viewport-height chart containers [@style]. This makes page edits risky when reordering sections, changing video aspect ratios, or making mobile layouts. The README's stated future work on universal CSS and smartphone-friendly fonts is not cosmetic; it targets this coupling [@style].

## Markup Contracts

`categorySelector` must exist before `js/main.js` runs because `main.js` reads `document.getElementById('categorySelector').value` at load time [@index].

`slider` must exist because `createBarGraphSlider` calls `noUiSlider.create` on that element during visualization setup [@index].

`reset-button` must exist because `NetworkGraph.updateVis` attaches a click listener to it when the force graph is built [@index].

The map section must keep both toss bar containers because `MapVis` updates global `indiaMapCoinTossDecision` and `indiaMapCoinTossWinner` instances after map clicks [@index].
