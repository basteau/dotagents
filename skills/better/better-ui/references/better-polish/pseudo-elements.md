# Pseudo-elements

Decorative layers on `::before`/`::after`, native pseudo-elements, and View Transitions
API mechanics. Durations, easing and reduced-motion behavior for anything that moves
belong to [better-animation](../better-animation.md).

## Before and after

Use a pseudo-element for decoration that belongs to one element: a hover background, a
glow, a scrim, a second shadow, an extended hit area. Use a real element when the
content is meaningful, interactive, needs its own events or is driven from JavaScript.

| Requirement | Why |
| --- | --- |
| `content: ""` | Without `content` the pseudo-element does not render |
| `position: relative` on the parent | An absolute pseudo-element otherwise positions against a distant ancestor |
| `isolation: isolate` on the parent, `z-index: -1` on the layer | Paints the layer above the parent's background and below its content |
| `border-radius: inherit` | The layer follows the parent's corners |
| `pointer-events: none` | Decoration must not swallow clicks; drop it only for hit-area layers |

```css
.button {
  position: relative;
  isolation: isolate;
}
.button::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: inherit;
  background: var(--surface-hover);
  opacity: 0;
  pointer-events: none;
  z-index: -1;
}
@media (hover: hover) {
  .button:hover::before { opacity: 1; }
}
```

Replaced and void elements (`img`, `input`, `video`, `iframe`, `br`) render no
`::before`/`::after`; put the layer on a wrapper. Text in `content` is read by screen
readers. Give decorative glyphs empty alternative text, `content: "→" / ""`, or keep
them out of `content`.

### Hover backgrounds that bleed past the box

A ghost button or nav link can keep its label aligned to the layout grid while its
hover pill extends beyond it. Use a negative inset on the layer instead of padding the
element, so nothing shifts:

```css
.nav-link::before {
  content: "";
  position: absolute;
  inset: -4px -8px;
  border-radius: 8px;
  background: var(--surface-hover);
  opacity: 0;
  pointer-events: none;
  z-index: -1;
}
```

Check the bleed against neighbors and the container edge so it never clips or overlaps.

The same negative-inset layer with pointer events left on enlarges a small target; sizes
and the collision rule are in [hit-areas.md](../better-accessibility/hit-areas.md).

### Elevation on hover

Transitioning a large, multi-layer `box-shadow` repaints the shadow every frame. Put the
raised shadow on a pseudo-element and fade its opacity, which composites cheaply:

```css
.card {
  position: relative;
  isolation: isolate;
  box-shadow: var(--shadow-1);
}
.card::after {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: inherit;
  box-shadow: var(--shadow-3);
  opacity: 0;
  pointer-events: none;
  z-index: -1;
}
@media (hover: hover) {
  .card:hover::after { opacity: 1; }
}
```

A small ring change, such as the shadow-border hover in [surfaces.md](surfaces.md#shadow-rings),
can transition `box-shadow` directly. Add the opacity transition with the project's timing.

## Native pseudo-elements

Style the browser's own parts instead of rebuilding them.

| Pseudo-element | Replaces | Notes |
| --- | --- | --- |
| `::backdrop` | An overlay `<div>` behind a modal | Applies to `dialog.showModal()`, popovers and fullscreen. Clicks on it target the dialog itself, so with an unpadded dialog and an inner wrapper, `event.target === dialog` means outside |
| `::placeholder` | A conditional `<span>` over the input | Set `opacity: 1` (Firefox lowers it); a placeholder is a hint, never the label |
| `::selection` | Script-driven highlight markup | Only `color`, `background-color`, text decoration and `text-shadow` apply; keep selected text legible on every surface |
| `::marker` | `list-style: none` plus a background-image bullet | Accepts color, font properties and `content` |
| `::file-selector-button` | A hidden file input behind a custom button | Restyle the native button and keep the input's behavior |

```css
dialog::backdrop {
  background: oklch(0 0 0 / 0.4);
  backdrop-filter: blur(4px);
}
input::placeholder {
  color: var(--text-placeholder);
  opacity: 1;
}
::selection {
  background: var(--selection-bg);
  color: var(--selection-fg);
}
li::marker {
  color: var(--text-tertiary);
}
```

Older engines do not inherit custom properties into `::backdrop`; give `var()` a
fallback there. Entry and exit animation for dialogs and their backdrop is in
[better-animation](../better-animation.md). Placeholder contrast and modal focus belong to
[better-accessibility](../better-accessibility.md); selection, `::first-line` and other
text pseudo-elements are in
[better-typography](../better-typography/details-and-accessibility.md).

## View transitions

The browser snapshots the old view, runs your DOM update, snapshots the new one and
animates between them on a pseudo-element tree above the page:

```
::view-transition
└─ ::view-transition-group(name)       size and position
   └─ ::view-transition-image-pair(name)
      ├─ ::view-transition-old(name)   outgoing snapshot, fades out
      └─ ::view-transition-new(name)   incoming snapshot, fades in
```

Without names, the whole page cross-fades as `root`.

### Choosing it

| Use view transitions | Keep the motion library |
| --- | --- |
| Route or page changes, including same-origin multi-page sites where both pages opt in with `@view-transition { navigation: auto; }` | Gesture-driven motion that follows a pointer |
| Shared-element moves where the DOM node is replaced, such as thumbnail to lightbox | Animations the user may reverse mid-flight; a new view transition skips the running one |
| List reorders and filter results | In-component layout animation already built on the library |

Feature-detect and fall back to an instant update, never to a broken state.

### Naming

A name marks an element as its own group. Each name must be unique among rendered
elements when a snapshot is taken, or the transition is skipped. Assign it only to the
element that moves, hand it from old element to new inside the update, and clear it when
the transition finishes:

```ts
function openLightbox(thumb: HTMLElement, update: () => void) {
  if (!document.startViewTransition) return update();

  thumb.style.viewTransitionName = "hero";
  const transition = document.startViewTransition(() => {
    thumb.style.viewTransitionName = "";
    update(); // renders the lightbox image, which takes the name:
    document.querySelector<HTMLElement>(".lightbox img")!.style.viewTransitionName = "hero";
  });
  transition.finished.finally(() => {
    document.querySelector<HTMLElement>(".lightbox img")?.style.removeProperty("view-transition-name");
  });
}
```

A static `view-transition-name` in a rule that matches several cards breaks uniqueness;
derive names per item (`card-${id}`) or set them only on the active one. Group many
names under one style with `view-transition-class`.

### Styling

```css
::view-transition-group(hero) {
  animation-duration: var(--duration-medium);
  animation-timing-function: var(--ease-out);
}

/* Snapshots stretch when the aspect ratio changes; fill and clip instead */
::view-transition-old(hero),
::view-transition-new(hero) {
  height: 100%;
  overflow: clip;
}

/* Reduced motion: drop movement and resizing, keep the cross-fade */
@media (prefers-reduced-motion: reduce) {
  ::view-transition-group(*) {
    animation: none;
  }
}
```

Use the project's duration and easing tokens; choose them per
[better-animation](../better-animation.md). Keep view transitions short, since the
snapshot layer sits over live content until it finishes.

### React (optional)

Commit state synchronously inside the callback, as in
`document.startViewTransition(() => flushSync(() => setSelected(item)))`, so the new
snapshot sees it. Where the project's router has built-in view-transition support, use it.
