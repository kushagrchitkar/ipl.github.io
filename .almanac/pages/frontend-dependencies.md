---
title: Frontend Dependencies
summary: The app depends on CDN-loaded D3, fullPage.js, noUiSlider, Bootstrap, and Google Fonts, with no local package lock or vendored fallback.
topics: [frontend, performance]
sources:
  - id: index
    type: file
    path: index.html
    note: Lists all external CSS and script dependencies and their load order.
  - id: style
    type: file
    path: css/style.css
    note: Shows dependency on fullPage navigation classes and Google Font families.
  - id: main
    type: file
    path: js/main.js
    note: Uses d3 and noUiSlider globals.
status: active
verified: 2026-06-18
---

This project has no package manager metadata. All third-party browser dependencies are loaded directly from CDNs in `index.html` [@index]. That makes the website easy to host as static files, but it also means dependency versions are controlled by script/link tags rather than a lockfile.

The current external dependencies are:

| Dependency | Loaded as | Project use |
| --- | --- | --- |
| Bootstrap | CSS 5.2.1, JS bundle 5.1.3 | Grid classes and basic layout utilities in `index.html` |
| noUiSlider 15.4.0 | CSS and JS | Batsman top-N slider created in `main.js` |
| fullPage.js 4.0.20 | CSS and JS | Full-screen section scrolling and right-side navigation |
| Google Fonts | Bebas Neue, Domine | Heading, SVG text, and body typography |
| D3 v7 | JS | All chart rendering, scales, axes, aggregation, force simulation, and geo projection |

Because dependencies are globals, local scripts do not import or guard them. `main.js` calls `d3.csv`, `d3.json`, `d3.timeParse`, and `noUiSlider.create` directly [@main]. Chart classes call D3 APIs directly. If a CDN load fails or script order changes, the runtime fails before charts can recover [@index].

The CSS also assumes third-party class names. The fullPage navigation bullets are restyled by targeting `.fp-right ul li a span` and replacing the default marker background with `img/cricketball.png` [@style]. A fullPage.js upgrade that changes navigation markup can silently break this styling.

## Maintenance Implications

When changing dependency versions, verify the page by scrolling through all sections and checking the batsman slider, the D3 charts, the force graph reset, and fullPage navigation. There is no automated dependency compatibility test in the repo.

If offline or deterministic builds become important, the project needs either vendored assets or a package-managed build step. That would be an architectural change from the current static-CDN model described in [[project-shape]].
