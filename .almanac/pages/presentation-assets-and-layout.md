---
title: Presentation Assets And Layout
summary: The site relies on full-screen video sections, absolute overlays, Bootstrap columns, and fixed chart container heights, making visual polish and performance tightly tied to media assets and CSS positioning.
topics: [frontend, media, layout, performance]
sources:
  - id: readme
    type: file
    path: README.md
    note: Records video compression, laptop-oriented CSS, and smartphone font plans.
  - id: index
    type: file
    path: index.html
    note: Shows video-backed sections, story content, Bootstrap grid usage, and visualization containers.
  - id: style
    type: file
    path: css/style.css
    note: Defines video object-fit behavior, overlay positioning, chart dimensions, text-box styles, and fonts.
  - id: image-dir
    type: file
    path: img/
    note: Contains the local video backgrounds and cricketball navigation asset used by the dashboard.
status: active
verified: 2026-06-18
---

The site’s first impression and transitions are media-heavy. `index.html` uses looping muted videos as full-screen backgrounds for the hero, cricket introduction, IPL context, CSK/MI segment, batsman segment, umpire segment, humidity segment, and closing section [@index]. `css/style.css` positions `.myVideo` absolutely over the section, stretches it to full width and height, and uses `object-fit: cover` with a black fallback [@style].

The README says this alternate repository compressed the original video backgrounds to improve load time, and it records two known presentation constraints: CSS effects are currently tailored for MacBook/Linux laptops, and the font is not yet smartphone-friendly [@readme]. Future visual work should treat those as current product constraints rather than accidental comments.

## Layout Model

The page uses fullPage.js sections as the top-level layout and Bootstrap rows/columns inside sections [@index]. Text overlays are usually absolute-positioned `.layer`, `.layer1`, `.layer2`, or section-specific variants with z-index above the videos [@style]. Narrative text often sits inside translucent `.text-box` or `.text-box2` elements [@style].

Chart containers have fixed or viewport-relative dimensions in CSS:

- `#barDiv` and `#lineDiv` are `70vh`.
- `#network-graph` is `1000px` tall.
- `#mapDiv` is `80vh`.
- `#coin-toss-decision-bar` and `#coin-toss-winner-bar` are `40vh`.

Visualization classes read these dimensions at construction and do not listen for resize events [@style]. CSS changes to section size, Bootstrap column widths, font loading, or mobile breakpoints can affect chart geometry at initialization time.

## Navigation Styling

The fullPage right-side navigation bullets are restyled with `img/cricketball.png` through `.fp-right ul li a span` [@style]. This means a missing or large cricketball image affects navigation affordance, not just decoration.

## Maintenance Notes

The CSS repeats several section-specific layer blocks and duplicates some selectors such as `#section1 .layer` and `#ipl li` [@style]. That is not a runtime bug by itself, but agents should expect visual changes to be spread across section ids rather than a compact component system.

Because videos are loaded directly from `img/*.mp4` and several are large enough to materially affect initial page weight, replacing or adding video backgrounds should include a file-size check and browser playback check [@image-dir].

