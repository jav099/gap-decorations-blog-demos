# CSS Gap Decorations Blog Demos

Demos for the Chrome 149 gap decorations blog post. Hosted on GitHub Pages.

## Spec and references

- Spec: https://www.w3.org/TR/css-gaps-1/
- Dev trial blog post: https://developer.chrome.com/blog/gap-decorations
- Edge explainer: https://github.com/MicrosoftEdge/MSEdgeExplainers/blob/main/CSSGapDecorations/explainer.md

## Directory structure

```
├── README.md              # Blog post markdown
├── index.html             # Gallery landing page (links to demos)
├── demos/                 # Self-contained HTML demos (each has own styles/fonts/JS)
│   ├── split-screen.html  # Flex, dashed/solid rule styles, responsive
│   ├── settings-list.html # Flex column, grouped separators with repeat()
│   ├── dashboard-grid.html# Grid, animated rule colors/insets on hover
│   ├── calendar-week.html # Grid, alternating solid/dashed row rules
│   ├── dynamic-items.html # Flex wrap, add/remove/resize items, JS interactive
│   └── photo-gallery.html # Grid with empty cells, rule-visibility-items toggle
└── screenshots/           # PNGs used in README and index.html
```

## Key CSS properties used across demos

- `column-rule`, `row-rule`, `rule` shorthands
- `*-rule-width`, `*-rule-style`, `*-rule-color` (comma-separated lists per spec)
- `repeat()` for varying decorations across gaps
- `*-rule-break`: `normal`, `none`, `intersection`
- `rule-inset`, `rule-inset-cap`, `rule-inset-junction` (values: lengths, `overlap-join`)
- `rule-visibility-items`: `normal`, `all`, `around`, `between`
- `rule-overlap`: `row-over-column`, `column-over-row`

## Important

- Multi-value lists for width, style, and color are **comma-separated** (spec uses `#` multiplier).
- Inset properties are NOT list-valued (can't vary per gap).
- Each demo is fully self-contained. No shared CSS or JS between demos.
- The gallery index.html uses screenshots as thumbnails linking to each demo.
