# Rendering

Recipes for keeping style, layout and paint cheap, and for shipping less CSS. Measure
in the browser's performance tools before and after; a change that does not move a
measurement is not a fix. Motion cost and timing are
[better-animation](../better-animation.md)'s; font loading is
[better-typography](../better-typography.md)'s.

## Animate what the compositor can handle

```css
.drawer {
  translate: 100% 0;
  transition: translate 240ms var(--ease-out, ease-out), opacity 240ms;
}
.drawer[data-open] { translate: 0 0; }
```

- `transform`, the individual `translate`, `scale` and `rotate`, `opacity` and
  usually `filter` skip layout and often paint. `width`, `height`, `inset`, `margin`
  and `top` trigger layout on every frame.
- Name each transitioned property. `transition: all` animates changes nobody planned
  and costs work on every state change.
- To fade a large shadow, transition the `opacity` of a pseudo-element that holds the
  target shadow instead of the `box-shadow` itself.
  [better-polish](../better-polish.md) owns the shadow values.
- `will-change` is a last resort for a measured jank, set just before the animation
  and removed after; on many elements it costs memory.

## Skip work for off-screen content

```css
.article-section {
  content-visibility: auto;
  contain-intrinsic-size: auto 40rem;
}

.widget { contain: layout paint; }
```

- `content-visibility: auto` skips layout and paint for sections far from the
  viewport. The reserved size prevents scrollbar jumps; `auto` lets the browser
  remember the real size after first render.
- Use it on long pages with heavy, independent sections. On short pages or small
  items it adds bookkeeping without a gain. Content stays in the accessibility tree
  and find-in-page.
- `contain: layout paint` tells the browser a widget's internals do not affect the
  outside. It also creates a stacking context and clips paint, so check overflowing
  tooltips and shadows.

Support: `content-visibility` in Chrome 85, Firefox 125, Safari 18.

## Keep style recalculation local

- Selector matching is rarely the bottleneck; flat class selectors are both the
  fastest and the most maintainable choice anyway.
- Broad `:has()` anchors such as `body:has(…)` or `*:has(…)` make every DOM change
  re-check the whole page. Anchor `:has()` to the nearest relevant element.
- Changing an inherited custom property on `:root` restyles the whole tree. Set
  frequently changing values, such as pointer position, on the element that uses
  them, and register them with `inherits: false` where possible.
- Prefer CSS scroll and view timelines for scroll-linked effects over scroll
  listeners that write styles each frame. Check engine support; without it the effect
  should simply be absent.

## Ship less CSS

- Bundle `@import` chains in the build, or replace them with `<link>` elements; a CSS
  `@import` is discovered only after its parent downloads.
- Load styles a route does not need with that route, and keep render-blocking CSS to
  what the first view uses.
- Delete dead rules when you remove markup; a layer or file per component makes
  ownership obvious.
- Let the build minify and add the vendor prefixes the browser targets need, instead
  of writing prefixes by hand. Keep the ones no tool adds, such as
  `-webkit-line-clamp` with its required companions.
