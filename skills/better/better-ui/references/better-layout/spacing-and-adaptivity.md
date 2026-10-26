# Spacing and adaptivity

Recipes for the spacing scale, control clearance, edge insets, disclosure cues, edge
behavior, breakpoints and growth.

## Space on one scale

Use the project's spacing tokens. Where none exist, define a small scale once and
take every gap, padding and margin from it:

```css
:root {
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-5: 24px;
  --space-6: 32px;
  --space-7: 48px;
}

/* Good: every value is a step */
.card { padding: var(--space-4); }
.stack { gap: var(--space-3); }

/* Bad: near-miss values nobody chose on purpose */
.card { padding: 17px; }
.stack { gap: 13px; }
```

- Steps widen as they grow, so neighbouring levels of hierarchy stay distinguishable.
  Two values one or two pixels apart read as a mistake.
- An off-scale value is a finding only when it breaks grouping or a shared edge.
  Optical one-offs, hairlines and borders are not spacing.
- Nested radius follows padding; that rule is [better-polish](../better-polish.md)'s.

## Borderless controls need more clearance

| Between | Starting point |
| --- | --- |
| Adjacent bordered or filled controls, such as buttons and inputs | `12px` |
| Visible glyphs of borderless controls, such as text and icon buttons | `24px` |
| Unrelated control groups | `24px` or more |

Nothing marks where one borderless target ends and the next begins, so the space is
the boundary. Measure between what the user sees: a borderless button's own padding
counts toward the `24px`. Preserve an established, usable density rather than
expanding controls to match these values.

```css
/* Good: bordered buttons at 12px, borderless icon buttons at 24px between glyphs */
.button-row { display: flex; gap: 12px; }
.icon-row { display: flex; gap: 8px; }        /* plus 8px padding per side = 24px */
.icon-row button { padding: 8px; }

/* Bad: borderless icon buttons packed at 4px with no padding */
.icon-row { display: flex; gap: 4px; }
.icon-row button { padding: 0; }
```

Target sizes and hit-area expansion belong to
[better-accessibility](../better-accessibility.md). Space controls so their expanded
hit areas never overlap.

## Inset buttons from the edges

A button pressed against the viewport looks like system chrome and clips against
curved corners and gesture zones.

```css
/* Good: inset action bar */
.action-bar {
  padding-inline: 16px;
  padding-bottom: calc(16px + env(safe-area-inset-bottom));
}
.action-bar button { width: 100%; }

/* Bad: button glued to three edges, and 100vw adds the scrollbar width */
.action-bar button {
  width: 100vw;
  position: fixed;
  bottom: 0;
}
```

The button can still span the full content width inside the margins. Start near
`16px` inline on mobile where the project has no layout token.

## Hint at hidden content

Keep the product's established scroll indicator or disclosure pattern; use these only
where no cue exists.

- **Peeking items.** A row of cards that ends exactly at the container edge looks
  complete, and nobody scrolls it.
- **Disclosure controls.** A collapsed section gets a chevron or a "Show more"
  control naming what is hidden: "Show 12 more results", not "More".
- **Truncated values.** Pair the clamp with an expand control, a link to a detail
  view or a tooltip that also opens on keyboard focus. A hover-only `title` attribute
  does not count, because keyboard and touch users never see it. Clamp mechanics are
  [better-typography](../better-typography.md)'s.

The item width sets the peek. With `100%` as the scroller's content box, item width =
`100%` + inline padding − gap − peek. Scroll padding equal to the inline padding
keeps snapped items on the content edge.

```css
.scroller {
  display: flex;
  gap: 12px;
  overflow-x: auto;
  padding-inline: 24px;
  scroll-padding-inline: 24px;
  scroll-snap-type: x mandatory;
}
.scroller > * {
  flex: 0 0 calc(100% - 12px); /* 100% + 24px padding - 12px gap - 24px peek */
  scroll-snap-align: start;
}
```

## Content bleeds, controls float

- **Content layer**: backgrounds, hero media and scrollable lists extend to the
  viewport edges.
- **Control layer**: text and controls stay inside the layout margins and safe areas,
  floating above the content.

```css
/* Good: full-bleed media inside a constrained article */
.article {
  display: grid;
  grid-template-columns: 1fr min(65ch, calc(100% - 48px)) 1fr;
}
.article > * { grid-column: 2; }
.article > .full-bleed { grid-column: 1 / -1; }
```

Safe-area insets resolve to `0` unless the viewport meta opts in, and they are
physical. A floating button pairs each physical side with its own inset and overrides
it in RTL:

```html
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
```

```css
.fab {
  position: fixed;
  right: calc(16px + env(safe-area-inset-right));
  bottom: calc(16px + env(safe-area-inset-bottom));
}
[dir="rtl"] .fab {
  right: auto;
  left: calc(16px + env(safe-area-inset-left));
}

/* Sticky header never covers an anchor target or a focused element */
html { scroll-padding-block-start: var(--header-height); }
```

Landscape insets, nested chrome and keyboard clearance are
[better-pwa](../better-pwa.md)'s.

## Hold structure until it breaks

Break where the layout stops fitting, not at `768px` because a preset says so: where
the sidebar squeezes content below its minimum measure, or a card grid drops below a
usable column width. Collapsing late keeps the layout stable and familiar, and
premature collapsing throws away space the user has.

```css
/* Good: component adapts to its container */
.card-list { container-type: inline-size; }
@container (width < 400px) {
  .card { grid-template-columns: 1fr; }
}

/* Bad: viewport media query breaks the card inside a narrow sidebar */
@media (max-width: 768px) {
  .card { grid-template-columns: 1fr; }
}
```

A query styles only descendants of the container, never the container itself.
`container-type: inline-size` adds size containment, so the container no longer takes
its width from its content. A shrink-to-fit container such as a flex item with no
width, an inline-block or an absolutely positioned box collapses to zero.

Grids that fit as many columns as the space allows need no breakpoint at all:
`grid-template-columns: repeat(auto-fill, minmax(min(100%, 16rem), 1fr))`. The inner
`min()` keeps a single column from overflowing a container narrower than `16rem`.

Test the smallest and largest supported sizes first, since those break first, then
the sizes between.

## Plan for growth and clipping

```css
/* Good: label defines the size, and the row wraps */
.button { padding-inline: 16px; max-width: 100%; }
.button-row { display: flex; flex-wrap: wrap; gap: 12px; }

/* Bad: German will overflow or truncate */
.button { width: 96px; overflow: hidden; }
```

String expansion varies by language and by source-string length, so never budget one
universal percentage. Test with pseudo-localization and a long-string locale.

A grid track or flex child refuses to shrink below its content's minimum width, so
long words and wide tables push the layout past the viewport. Use
`grid-template-columns: minmax(0, 1fr)` and `min-width: 0` on flex children.

Fixed-height containers clip text at 200% zoom and in long locales. Use `min-height`
where a floor is needed. Where a box must cap its height, pair `max-height` with
`overflow-y: auto`.

Typical clip-prone spots are the bottom edge of a resizable pane, the bottom of a
modal taller than the viewport and anything the on-screen keyboard covers:

- Size full-height panes with `100dvh` or `100svh`, not `100vh`, which ignores mobile
  browser toolbars. [better-pwa](../better-pwa.md) owns which one.
- Give a modal `max-height: 100dvh`, a scrolling body and an action row outside the
  scroll area.
- Keep primary actions in stable chrome, such as a sticky footer with safe-area
  padding or the top of the view.
