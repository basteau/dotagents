# Layout mechanics

Recipes for intrinsic grids, the page grid, sibling spacing, overlap, container
queries and overflow. Which spacing values to use and where a layout should break are
[better-layout](../better-layout.md)'s.

## Size intrinsically before adding breakpoints

```css
/* Equal tiles: as many columns as fit, no column count per breakpoint */
.tiles {
  display: grid;
  gap: var(--space-4);
  grid-template-columns: repeat(auto-fit, minmax(min(100%, 16rem), 1fr));
}

/* A pair that wraps on its own: aside at its ideal width, main takes the rest */
.with-aside {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-4);
}
.with-aside > .aside { flex: 1 1 18rem; }
.with-aside > .main { flex: 999 1 0; min-inline-size: 55%; }
```

- `min(100%, 16rem)` keeps a single column from overflowing a container narrower than
  the minimum.
- `auto-fit` stretches a short last row across the width; `auto-fill` keeps empty
  tracks, so cards hold their size in a filtered list.
- The pair wraps when `.main` would drop below its `min-inline-size`, a share of the
  container rather than a viewport width. A sticky aside also needs `align-self:
  start`.

Support: everywhere with grid and flex `gap` (Chrome 84, Firefox 63, Safari 14.1).

## Lay out pages with one named grid

```css
.page {
  --gutter: var(--space-4);
  --content: 66ch;
  --breakout: 80rem;
  --breakout-extra: calc((var(--breakout) - var(--content)) / 2);

  display: grid;
  grid-template-columns:
    [full-start] minmax(var(--gutter), 1fr)
    [breakout-start] minmax(0, var(--breakout-extra))
    [content-start] min(100% - 2 * var(--gutter), var(--content)) [content-end]
    minmax(0, var(--breakout-extra)) [breakout-end]
    minmax(var(--gutter), 1fr) [full-end];
}

.page > * { grid-column: content; }
.page > .breakout { grid-column: breakout; }
.page > .full {
  grid-column: full;
  display: grid;
  grid-template-columns: inherit; /* its own children land back in the content column */
}
```

- Every direct child must be placed, or it lands in the first gutter track. Wrap
  loose inline content in one element.
- Change widths by overriding `--content` or `--breakout` on a section, not by
  writing a new template.
- Where the authoring system cannot target children, each child sets its own
  `grid-column`.

## Let the parent own space between children

```css
/* Known children: the parent sets one gap */
.form-fields {
  display: flex;
  flex-direction: column;
  gap: var(--space-3);
}
.form-fields > button { align-self: start; } /* a column stretches its children */

/* Content you do not control: zero the margins, then space each following sibling */
.flow > * { margin-block: 0; }
.flow > * + * { margin-block-start: var(--flow-space, 1em); }
.flow > :is(h2, h3) { --flow-space: 1.75em; }
.flow > :is(h2, h3) + * { --flow-space: 0.5em; }
```

- Keep the `>`; without it the rule reaches nested elements too, down to list items.
- The flow form counts hidden children and leaves a gap above a hidden first child;
  `gap` ignores them.
- Adjust the space where two specific sections meet with a sibling rule, `.hero +
  .logos` or `.logos:has(+ .cta)`, instead of spacer elements or per-page modifier
  classes. Add a pair rule only where the default spacing visibly fails.
- To push one item to the far end of a flex row or column, give it
  `margin-inline-start: auto` or `margin-block-start: auto` instead of a spacer or
  `space-between` across several children.

## Overlap and align with grid

```css
.hero-media { display: grid; }
.hero-media > * { grid-area: 1 / 1; }
.hero-media > .caption { place-self: end start; }

/* Cards whose title, body and footer line up across a row */
.cards {
  display: grid;
  gap: var(--space-5);
  grid-template-columns: repeat(auto-fit, minmax(min(100%, 18rem), 1fr));
}
.cards > .card {
  display: grid;
  grid-row: span 3; /* one row per part */
  grid-template-rows: subgrid;
  row-gap: var(--space-2);
}
```

- In a grid stack, later layers paint on top; use `z-index` only to change that
  order. The container sizes to its largest layer.
- `place-self` positions a layer in the cell; without it the layer stretches.
- A subgrid card needs `grid-row: span N` matching its part count, and its own
  `row-gap`, or it inherits the parent's gap.

Support: `subgrid` in Chrome 117, Firefox 71, Safari 16.

## Query the container in reusable components

```css
.slot { container: slot / inline-size; }

.promo-title { font-size: clamp(1.25rem, 1rem + 2cqi, 2rem); }

@container slot (width > 32rem) {
  .promo {
    display: grid;
    grid-template-columns: 10rem 1fr;
  }
}
```

- An element cannot query itself; the container is an ancestor. Making stable regions
  such as `header`, `main`, `aside` and `footer` containers covers most components.
- Size containment stops the container taking its width from its content. A
  shrink-to-fit container collapses; [better-layout](../better-layout.md) lists the
  cases.
- A container cannot also be a subgrid of its parent; use separate elements.
- With no container above it, `cqi` falls back to the small viewport size, so one
  `cqi` token works on the page and in every slot. Do not register such a token with
  `@property`: a registered length computes once where it is declared, usually
  `:root`.
- Name containers when components nest, so a query reaches the intended ancestor.
  Style queries, `@container style(--variant: compact)`, are not in every engine;
  check targets before relying on them.

Support: size queries and units in Chrome 105, Firefox 110, Safari 16.

## Let children shrink, then clip

```css
.toolbar {
  display: flex;
  overflow-x: auto;
  justify-content: safe center; /* falls back to start when the row overflows */
}

.banner { overflow-x: clip; } /* cuts overflow without creating a scroll container */
```

- `overflow: clip` does not create a scroll container, so `position: sticky` inside
  it keeps working and nothing can scroll it by script or focus. Use `hidden` or
  `auto` when a script scrolls the element or it has `resize`.
- Clip the overflowing element itself; clipping `html` or `body` hides the symptom
  page-wide.
- `safe` works on every `align-*`, `justify-*` and `place-*` property. Auto margins
  on the item give the same safe centering in older engines.

Support: `overflow: clip` in Chrome 90, Firefox 81, Safari 16. `safe` in Chrome 115,
Firefox 63, Safari 17.6.
