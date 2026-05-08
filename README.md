# Mind the Gap! CSS gap decorations available in Chrome 149

**Authors:** Javier Contreras, Sam Davis Omekara Jr
**Published:** 2026-05-06

If you've used borders or pseudo-elements to draw lines between grid or flex items, there's a native alternative now. CSS gap decorations shipped in Chrome and Edge 149. You can now  style the gaps in grid, flexbox, and multi-column layouts without any extra markup.

This feature was built in collaboration between the Microsoft Edge and Google Chrome teams, based on the [CSS Gap Decorations specification](https://www.w3.org/TR/css-gaps-1/).

## Useful links

- [CSS Gap Decorations specification](https://www.w3.org/TR/css-gaps-1/)
- [Developer trial blog post](https://developer.chrome.com/blog/gap-decorations) (Chrome 139)
- [Edge announcement](https://blogs.windows.com/msedgedev/2025/03/19/minding-the-gaps-a-new-way-to-draw-separators-in-css/)
- [Explainer on GitHub](https://github.com/MicrosoftEdge/MSEdgeExplainers/blob/main/CSSGapDecorations/explainer.md)

## The problem

Styling gaps between layout items has always required workarounds. Borders on individual items, pseudo-elements, background hacks. These approaches have costs:

- **They depend on structure.** Adding a visual separator means changing your markup or writing selectors for specific items.
- **They interfere with accessibility.** Extra DOM elements show up in the accessibility tree when they shouldn't.
- **They're hard to maintain.** Responsive layouts break the assumptions your gap styling was built on.
- **They hurt performance.** More DOM nodes, more layout work.
- **They have poor authoring ergonomics.** Complex geometric calculations were often needed to get things working correctly.

The column-rule property already handles this for multi-column layouts. But grid and flexbox had no equivalent functionality.

## The solution

Grid and flexbox containers now accept `column-rule`, which previously only worked in multi-column layouts. A new `row-rule` property handles horizontal gaps. The resulting decorations are purely visual and won't affect your existing layouts. If you already use `column-rule` in multi-column, nothing changes for you.

For example, if you’d like to make use of this property in grid, simply set the `column-rule` and `row-rule` shorthands to include a rule width, style and color as shown in the following example:

```css
.grid {
  display: grid;
  gap: 1rem;
  column-rule: 1px solid #ccc;
  row-rule: 1px solid #ccc;
}
```

As illustrated in this example, both row-rule and column-rule accept the same shorthand for width, style, and color. And you can even use the rule shorthand to set both at once.

### New properties

However, we didn't just bring `column-rule` to other layout modes and add the `row` counterpart. We also introduced controls for fine-tuning your decorations: how they break at intersections, how far they inset from gap edges, when they appear relative to items, and how to vary styles across gaps. Rule width, color, and insets are all animatable too.

### The repeat() syntax

Gap decorations support `repeat()` for their width, style, and color values. This lets you vary decorations across gaps in short form, similar to the `repeat()` syntax available when setting up your track definitions in grid:

```css
.grid {
  column-rule-width: repeat(2, 1px), 4px;
  column-rule-style: solid;
}
```

This gives the first two column gaps a 1px rule and the third a 4px rule, cycling if there are more gaps than values.

You can also pass multiple values directly without `repeat()`. For example, alternating row rule colors:

```css
.grid {
  row-rule: 1px solid;
  row-rule-color: red, blue;
}
```

This alternates between red and blue for each row gap.

### Controlling decoration breaks

The `column-rule-break` and `row-rule-break` properties control how decorations behave at gap intersections:

- `normal` (default) Behavior depends on the type of container. More information in the [spec](https://www.w3.org/TR/css-gaps-1/#breaks).
- `none`: decorations run continuously through intersections
- `intersection`: decorations break where row and column gaps cross

For example, if you set rule-break to intersection in a grid container, you will observe that rules break at gap intersections rather than continuing through:

```css
.grid {
  column-rule: 1px solid #5a9e9e;
  row-rule: 1px solid #c27a6b;
  rule-break: intersection
}
```

If you set rule-break to none, the rules will run continuously through intersections without breaking:

```css
.grid {
  column-rule: 1px solid #5a9e9e;
  row-rule: 1px solid #c27a6b;
  rule-break: none;
}
```

### Extending or shrinking decorations

The `column-rule-inset` and `row-rule-inset` properties control how far decorations extend within a gap. You can set insets on all sides at once with the shorthand, or target insets asymmetrically with`column-rule-inset-cap` (for endpoints that have no crossing gaps, like edges of container) and `column-rule-inset-junction` (for endpoints that intersect with other gaps). 

Values can be lengths, percentages, or the `overlap-join` keyword.

For example, setting `rule-inset` to 9px combined with `rule-break: intersection` will make all decorations "shrink" inwards 9px:

```css
.flex {
  display: flex;
  flex-wrap: wrap;
  column-rule: 1px solid #5a9e9e;
  row-rule: 1px solid #c27a6b;
  column-rule-inset: 9px;
  row-rule-inset: 9px;
  rule-break: intersection;
}
```

Setting `rule-inset-cap` to 16px and `rule-inset-junction` to 0px will give you "shrunken" decorations at container edges, but still break and be flush at intersections:


```css
.flex {
  display: flex;
  flex-wrap: wrap;
  column-rule: 1px solid #5a9e9e;
  row-rule: 1px solid #c27a6b;
  column-rule-inset-cap: 16px;
  column-rule-inset-junction: 0px;
  row-rule-inset-cap: 16px;
  row-rule-inset-junction: 0px;
}
```

### Visibility

`column-rule-visibility-items` and `row-rule-visibility-items` control when rules appear based on item adjacency:

- `normal` (default) depends on container type.
- `all`: rules appear in every gap, even empty ones
- `around`: rules appear wherever there are one or more adjacent items.
- `between`: rules appear only between two adjacent items

The `rule-visibility-items` shorthand sets both at once.

Setting `rule-visibility-items` to `all` will show rules in every gap, even those without adjacent items:

```css
.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  column-rule: 1px solid #5a9e9e;
  row-rule: 1px solid #c27a6b;
  rule-visibility-items: all;
}
```

On the other hand, setting `rule-visibility-items` to `between` will show rules only between adjacent items, hiding them in empty gaps:

```css
.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  column-rule: 1px solid #5a9e9e;
  row-rule: 1px solid #c27a6b;
  rule-visibility-items: between;
}
```

### Animating decorations

Rule width, color, and insets are animatable. You can transition them on hover or any other state change:

```css
.grid {
  column-rule: 1px solid #ccc;
  column-rule-inset: 0px;
  transition: column-rule-color 0.3s, column-rule-inset 0.3s;
}

.grid:hover {
  column-rule-color: tomato;
  column-rule-inset: 10px;
}
```

## Demos

All demos are available at [jav099.github.io/gap-decorations-blog-demos](https://jav099.github.io/gap-decorations-blog-demos/).

The [developer trial blog post](https://developer.chrome.com/blog/gap-decorations) includes several more demos, including an [interactive playground](https://microsoftedge.github.io/Demos/css-gap-decorations/playground.html), a [burger menu](https://microsoftedge.github.io/Demos/css-gap-decorations/burger-menu.html), a [notebook layout](https://microsoftedge.github.io/Demos/css-gap-decorations/notebook.html), and a [magazine-style layout](https://microsoftedge.github.io/Demos/css-gap-decorations/daily-css-news.html) with a sudoku grid.

### Split screen

A flex container with three panels using a list of styles: `dashed, solid` for the column rules. On narrow viewports, the layout switches to `flex-direction: column` and the same pattern applies to the row rules.

![Split screen demo on desktop](screenshots/split-screen-1.png)

![Split screen demo on mobile](screenshots/split-screen-2.png)

[Try it](https://jav099.github.io/gap-decorations-blog-demos/demos/split-screen.html)

### Settings list

A flex column where `repeat()` creates grouped separators. The pattern `repeat(2, 1px), 4px` cycles across all gaps, giving a thicker rule every third gap to visually separate groups without extra markup.

![Settings list demo](screenshots/settings.png)

[Try it](https://jav099.github.io/gap-decorations-blog-demos/demos/settings-list.html)

### Dashboard grid

A grid dashboard with alternating column rule colors and widths. On hover, the rules transition to a uniform color and the junction insets animate.

![Dashboard grid demo](screenshots/dashboard.png)

[Try it](https://jav099.github.io/gap-decorations-blog-demos/demos/dashboard-grid.html)

### Calendar week view

A weekly calendar grid with alternating row rules: solid for hour boundaries, dashed for half-hours.

![Calendar week view demo](screenshots/calendar.png)

[Try it](https://jav099.github.io/gap-decorations-blog-demos/demos/calendar-week.html)

### Dynamic flex items

A wrapping flex container where items can be added or removed. The decorations adapt automatically as items reflow. A width slider shows how the rules update as the container resizes.

![Dynamic flex items demo](screenshots/flex-items.png)

[Try it](https://jav099.github.io/gap-decorations-blog-demos/demos/dynamic-items.html)

### Article grid

A grid of articles with intentional empty cells, demonstrating `rule-visibility-items`. Toggle between `normal`, `all`, `between`, and `around` to see how rules appear or disappear around empty slots.

![Article grid demo](screenshots/article-grid.png)

[Try it](https://jav099.github.io/gap-decorations-blog-demos/demos/article-grid.html)

## What changed since the developer trial

If you tried gap decorations during the developer trial (Chrome 139), here's what changed:

- The feature ships by default, no flags needed
- Property names were updated to align with the spec (e.g., `column-rule-outset` and `row-rule-outset` became `column-rule-inset` and `row-rule-inset` sub-properties)
- The `column-rule-visibility-items` and `row-rule-visibility-items` properties were added
- Animation support was added.

## Browser support

| Browser | Support |
|---------|---------|
| Chrome  | 149+    |
| Edge    | 149+    |


## Try it

Gap decorations work today in Chrome 149. In browsers without support, gaps render normally with no decorations visible.

File bugs at the [Chromium issue tracker](https://issues.chromium.org/issues/wizard). For more background, see the [CSS Gap Decorations specification](https://www.w3.org/TR/css-gaps-1/).
