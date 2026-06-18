---
title: Humidity Toss Flow
summary: The humidity section maps Indian venue-city humidity and uses clicked cities to update two toss-decision bar charts.
topics: [data-visualization, data-model, frontend]
sources:
  - id: index
    type: file
    path: index.html
    note: Defines the humidity narrative, map container, and two toss bar chart containers.
  - id: main
    type: file
    path: js/main.js
    note: Loads remote India GeoJSON, match data, humidity data, and constructs MapVis plus two CoinTossBarGraph instances.
  - id: mapvis
    type: file
    path: js/mapVis.js
    note: Aggregates city humidity, draws the map points, and updates toss bar charts on click.
  - id: cointoss
    type: file
    path: js/CoinTossBarGraph.js
    note: Renders the reusable toss bar chart for decisions and toss-winner outcomes.
  - id: humidity
    type: file
    path: data/matches_humidity.json
    note: Provides match-level humidity, latitude, longitude, and toss fields.
  - id: matches
    type: file
    path: data/matches_cleaned3.csv
    note: Provides match-level toss fields for city filtering in MapVis.
status: active
verified: 2026-06-18
external_version: "HindustanTimesLabs india_state.json remote GeoJSON"
---

The humidity section asks whether weather conditions affect toss decisions and win rate. It renders an India map with city dots colored by average humidity, plus two bar charts that update when a city dot is clicked [@index].

`main.js` loads India state GeoJSON from `https://raw.githubusercontent.com/HindustanTimesLabs/shapefiles/master/india/state_ut/india_state.json`, loads `matches_cleaned3.csv`, and loads `matches_humidity.json` [@main]. It constructs the two `CoinTossBarGraph` instances first, then constructs `MapVis("mapDiv", data[4], data[1], data[5])` so the map can update those global bar chart instances [@main].

`MapVis` starts by calling `coinTossDecision("Chennai", colorBasedOnHumidity("88"))` and `coinTossWinner("Chennai", colorBasedOnHumidity("88"))` before drawing the map [@mapvis]. Chennai is therefore the default toss city for the bar charts.

The map groups `matches_humidity.json` by `City`, averages numeric humidity values, keeps the first latitude/longitude/elevation for each city, and draws a dot at the projected coordinate [@mapvis]. The India states are filled with the color for 50% humidity so city dots are visually comparable against a neutral baseline [@mapvis].

At draw time, map points exclude rows with non-numeric humidity and exclude `Sharjah`, `Dubai`, and `Abu Dhabi` because the geographic base map is India-only [@mapvis]. Other non-India cities in the humidity dataset are not explicitly excluded in code, so their projected coordinates may be unreliable if they pass the numeric humidity filter [@humidity; @mapvis].

Clicking a city dot computes a humidity-based color and updates both toss bar charts [@mapvis]. The first chart counts `TossDecision` values for all matches in `matches_cleaned3.csv` with that `City`; the second chart counts toss decisions only when the toss winner was also the match winner [@mapvis]. `CoinTossBarGraph` is reusable: `isDecision` changes its title and x-axis label, while `wrangleData` sorts decision labels alphabetically and redraws bars, labels, and axes [@cointoss].

## Historical Data Path

`MapVis.mergeData` documents the original humidity enrichment path. It expects city-level weather CSVs under `data/IndiaWeather/`, flattens them, matches entries to matches by city and date, and logs merged JSON for manual copying [@mapvis]. The current repository does not include `data/IndiaWeather/`, and `main.js` does not call `loadCitiesCSV`; the active product path is the precomputed `matches_humidity.json` file [@main].

## Risk Points

The remote GeoJSON is a runtime dependency. Opening the page without network access can prevent the map from initializing even though most project data is local [@main].

`matches_humidity.json` stores humidity and coordinates as strings, and `MapVis` parses them locally [@humidity; @mapvis]. New enriched data must preserve parseable numeric strings or actual numbers.

## Related Pages

Read [[ipl-data-model]] for the enriched humidity dataset, [[visualization-runtime]] for remote GeoJSON loading and global toss chart instances, and [[frontend-dependencies]] for the broader CDN/runtime dependency model.
