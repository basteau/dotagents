# States and native UI

Recipes for state selectors, native interactive elements, stacking and media boxes. Focus rings are [better-accessibility](../better-accessibility.md)'s; hover gating and press feedback are [better-pwa](../better-pwa.md)'s; enter and exit transitions are [better-animation](../better-animation.md)'s.

## Express state in selectors

```css
/* Parent and page state from content */
.field:has(:user-invalid) .field-hint { color: var(--color-danger-text); }
.post:has(> img:first-child) { grid-column: span 2; }
html:has(dialog:modal) { overflow: hidden; }

/* Widget state from ARIA, so visual and announced state cannot disagree */
.disclosure[aria-expanded="true"] .chevron { rotate: 180deg; }
.nav-link[aria-current="page"] { font-weight: 600; }

/* Validation only after the user has interacted */
input:user-invalid { border-color: var(--color-danger-border); }
```

- `:user-invalid` waits for interaction, unlike `:invalid`, which flags empty required fields on load. Pair the color with text; the server still validates.
- `:modal` matches only `showModal()`, so `show()` leaves page scrolling alone. Keep `scrollbar-gutter: stable` on `html` so the lock does not shift the layout.
- `:has()` cannot nest inside `:has()`. Anchor it to a specific element rather than `body` or `*`, so style invalidation stays local.
- One unrecognized selector invalidates the entire selector list, and the rule is lost. Put new pseudo-classes in their own rule, or inside `:is()`, which forgives unknown selectors.

Support: `:has()` in Chrome 105, Firefox 121, Safari 15.4. `:user-invalid` in Chrome 119, Firefox 88, Safari 16.5.

## Style native elements before rebuilding them

```html
<button popovertarget="sort-menu">Sort</button>
<div id="sort-menu" popover>…</div>

<details name="faq">
  <summary>Shipping</summary>
  <div class="faq-body">…</div>
</details>
```

```css
/* Anchored popover, flipping when it would overflow */
@supports (position-area: block-end) {
  [popover] {
    margin: 0;
    margin-block-start: var(--space-2);
    position-area: block-end span-inline-end;
    position-try-fallbacks: flip-block, flip-inline;
  }
}

/* Disclosure marker and body */
summary { list-style: none; }
summary::-webkit-details-marker { display: none; }
details::details-content { overflow: clip; }

/* Textarea that grows with its text */
textarea {
  field-sizing: content;
  min-block-size: 3lh;
  max-block-size: 12lh;
}
```

- `dialog` and `popover` render in the top layer, above every `z-index`, with `Escape` and focus handling built in. Light-dismiss comes with `popover`; on `dialog`, the newer `closedby="any"` adds it where supported, so keep a backdrop-click handler ([pseudo-elements.md](../better-polish/pseudo-elements.md#native-pseudo-elements)) as the fallback.
- In engines with anchor positioning, a popover opened with a button's `popovertarget` anchors to that button. One opened by script needs its source, `menu.showPopover({ source: button })` where supported, or an explicit `position-anchor`.
- Prefer `span-*` values in `position-area`; `center` can stop the fallbacks from flipping. Engines without anchor positioning ignore the `@supports` block and show the popover in the viewport center.
- Give sibling `<details>` the same `name` to keep one open at a time. Put padding on an inner element, never on `::details-content`, which stays visible when closed.
- Bound `field-sizing` with min and max sizes; past the maximum the textarea scrolls. Without support it stays a fixed, scrolling textarea.
- Animate opening and closing with `@starting-style` and `allow-discrete` as [enter-exit.md](../better-animation/enter-exit.md#web-entering-from-display-none) describes.

Support: `popover` is Baseline 2024. Anchor positioning, `::details-content` and `field-sizing` are newer and reached engines at different times; check each against the project's targets, and rely on the fallbacks above where one is missing.

## Contain stacking

```css
:root {
  --layer-raised: 1;
  --layer-sticky: 10;
  --layer-banner: 20;
}

.card { isolation: isolate; } /* inner z-index values stay inside the card */
.card-badge { z-index: 1; }

.site-header {
  position: sticky;
  inset-block-start: 0;
  z-index: var(--layer-sticky);
}
```

- `isolation: isolate` creates a stacking context without side effects; `transform`, `opacity` below 1, `filter` and `contain: paint` create one too, which is often why a `z-index` "stops working".
- Keep the page-level scale to a handful of named steps. Anything that must sit above everything belongs in the top layer, not at `z-index: 9999`.

## Give unknown media a box

```css
.cover {
  inline-size: 100%;
  block-size: auto;
  aspect-ratio: 3 / 2;
  object-fit: cover;
  background: var(--color-surface-muted);
}

.logo-tile {
  aspect-ratio: 1;
  object-fit: contain;
}
```

- One axis must be `auto` in CSS; a fixed height wins over the ratio. Keep `width` and `height` attributes on `img` so the box exists before the file loads, with `block-size: auto` so the attribute does not fix the height.
- `cover` crops, and `object-position` moves the crop. Switch to `contain` when nothing may be cropped: logos, packshots, diagrams.
- An avatar or thumbnail of fixed size inside flex needs width, height and `flex: none`, or the row stretches or squeezes it.
- Leave the placeholder background off images with transparency. Neutral outlines on images are [better-polish](../better-polish.md)'s.
