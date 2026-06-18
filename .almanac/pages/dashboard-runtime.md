---
title: Dashboard Runtime
summary: The dashboard runtime is a direct browser load path where CDN libraries, local scripts, global state, and fixed script order initialize all D3 visualizations after data promises resolve.
topics: [static-site, runtime, dependencies, data-visualization]
sources:
  - id: index
    type: file
    path: index.html
    note: Shows CDN dependencies, fullPage setup, visualization script order, and DOM containers.
  - id: main
    type: file
    path: js/main.js
    note: Shows Promise-based data loading, global state, data cleaning, and visualization construction.
  - id: style
    type: file
    path: css/style.css
    note: Shows runtime layout assumptions for chart containers and video sections.
status: active
verified: 2026-06-18
---

The runtime is intentionally simple: the browser downloads `index.html`, applies CDN CSS plus `css/style.css`, loads D3 v7, fullPage.js, Bootstrap JS, noUiSlider, every local visualization class, and finally `js/main.js` [@index]. There is no bundler or module system. Each visualization class is placed in the global scope by its script tag, and `main.js` constructs those classes by name [@index] [@main].

`main.js` loads these inputs in one `Promise.all`:

| Promise index | Input | Primary consumers |
| --- | --- | --- |
| `data[0]` | `data/IPL_Ball_by_Ball_2008_2022_cleaned.csv` | [[batsman-performance-flow]] line chart and slider setup. |
| `data[1]` | `data/matches_cleaned3.csv` | [[team-success-views]], [[umpire-network]], map city/toss filters, and batsman year joins. |
| `data[2]` | `data/batsmanData.json` | Batsman ranking bar chart. |
| `data[3]` | `data/pointstable.csv` | CSK/MI slope graph. |
| `data[4]` | Remote India state GeoJSON | India humidity map. |
| `data[5]` | `data/matches_humidity.json` | Humidity map dots and coin-toss bar charts. |

`cleanData()` only coerces selected fields in the ball-by-ball and match datasets. It converts ball `ID`, `innings`, `overs`, `ballnumber`, run fields, and `isWicketDelivery` to numbers, and match `ID`, `Date`, `Season`, and `Margin` to typed values [@main]. Other datasets are consumed mostly as loaded. `pointstable.csv` fields are coerced inside `SlopegraphVis`, while humidity, latitude, longitude, and elevation are parsed at map render time [@main].

## Script Order Contract

`index.html` must load visualization classes before `js/main.js`. `main.js` refers to `BarMapVis`, `TreeVis`, `SlopegraphVis`, `NetworkGraph`, `BarVis`, `lineGraph`, `CoinTossBarGraph`, and `MapVis` as global constructors [@index] [@main]. It also reads `#categorySelector` during global initialization, so the category dropdown must already exist in the DOM before `main.js` executes [@index] [@main].

The initialization order in `createVis()` is semantically significant:

1. `BarMapVis` is created before `TreeVis` because the treemap stores a reference to the bar chart and calls `updateClickedTeam()` on it.
2. `BarVis` is created before `lineGraph`; the bar chart click handler later assumes global `batsmanLineGraph` exists.
3. The two `CoinTossBarGraph` instances are created before `MapVis`; the map constructor immediately calls `coinTossDecision()` and `coinTossWinner()`, which invoke those global bar chart instances.

These are not dependency-injected modules. They are global variables used as an orchestration surface [@main]. See [[visual-state-coordination]] for the interaction-level consequences.

## External Runtime Dependencies

The site relies on Bootstrap 5 CSS/JS, noUiSlider 15.4.0, fullPage.js 4.0.20, Google Fonts, and D3 v7 from public CDNs [@index]. The map also depends on a remote India state GeoJSON URL hosted by HindustanTimesLabs [@main]. A network failure for the remote GeoJSON rejects the entire `Promise.all`, which prevents `createVis()` from running for all visualizations, not just the map [@main].

## Layout Timing

Every visualization class computes width and height from its parent element during construction. The CSS sets fixed viewport-height containers for the batsman charts, map, coin-toss charts, and network graph [@style]. If layout changes after construction, the classes do not have resize handlers, so SVG dimensions and scales do not automatically recompute.

