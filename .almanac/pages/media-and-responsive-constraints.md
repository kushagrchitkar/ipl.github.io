---
title: Media and Responsive Constraints
summary: The site's user experience is shaped by large full-screen video backgrounds, absolute overlay positioning, and README-documented device-readability issues.
topics: [frontend, performance]
sources:
  - id: readme
    type: file
    path: README.md
    note: Records video compression, laptop-specific CSS, smartphone font issues, and future optimization goals.
  - id: index
    type: file
    path: index.html
    note: Shows which sections use video backgrounds and how the story depends on media assets.
  - id: style
    type: file
    path: css/style.css
    note: Defines full-screen video behavior, overlay positioning, text boxes, and chart sizing.
status: active
verified: 2026-06-18
---

Pitch Perfect is media-heavy by design. The first screen and several narrative transitions use full-screen MP4 video backgrounds loaded from `img/` [@index]. The README says this alternate repository already compressed the original video backgrounds to improve load time [@readme].

The largest repo assets are videos: `ipl.mp4`, `tare.mp4`, `batsmen.mp4`, `csk vs mi.mp4`, `umpires.mp4`, and `rain.mp4` dominate repository size. The CSS sets `.myVideo` to absolute positioning, full width and height, `object-fit: cover`, black fallback background, and z-index 3, while text overlays sit above at z-index 4 [@style].

The layout is currently tuned for specific desktop environments. The README states that CSS effects are customized for MacBook/Linux laptops and that future updates should use a more universal CSS approach [@readme]. It also records that the existing font causes smartphone readability issues and should be replaced or adjusted for small screens [@readme].

Those README notes match the implementation. Many overlays use fixed percentages for `top`, `left`, `margin-right`, and `font-size`; chart containers use viewport-height dimensions such as `70vh`, `80vh`, and `40vh`; and section-specific rules hard-code positions for layers in `#section5`, `#section7`, `#section11`, and `#section13` [@style]. These choices can work for a target laptop viewport while breaking on narrow or short screens.

## Practical Checks

After changing media, CSS, or section order, manually inspect at least the opening hero, CSK/MI slide, batsman section, umpire section, humidity map, and closing slide. The high-risk failures are text overlays leaving the viewport, white text over bright video frames, chart SVGs receiving too-small parent dimensions, and fullPage navigation covering content.

Do not treat video compression as separate from product behavior. The videos are narrative assets and first-viewport signals, but load time and mobile readability are explicit maintenance goals for this alternate repository [@readme].

## Related Pages

Read [[narrative-page-shell]] for section and overlay structure, [[frontend-dependencies]] for CDN-loaded presentation dependencies, and [[project-shape]] for the repository's current optimization goals.
