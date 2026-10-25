# Viewport and scroll

Recipes for viewport units, safe areas, gesture ownership, overscroll, browser
chrome color, and installed mode. Replace example values with the project's tokens.

## One viewport declaration

```html
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover" />
```

Set it once, through the framework's metadata API where there is one. Add
`interactive-widget=resizes-content` only for keyboard-heavy shells (see
[touch-and-input.md](touch-and-input.md#keyboard-open-layout)). Never add
`user-scalable=no` or `maximum-scale=1`.

Next.js (optional): the `viewport` export carries these fields and `themeColor`
(`[{ media, color }]`); do not also hand-write the tags.

## Viewport units

| Unit | Height it measures | Use for |
| --- | --- | --- |
| `dvh` | The visible area, updating as browser chrome shows and hides | App shells, drawers, full-screen sheets |
| `svh` | The smallest viewport, chrome expanded | Heroes and first screens that must fit and stay stable |
| `lvh`, `vh` | The largest viewport, chrome collapsed | Rarely; overflows on load on mobile |

```css
.app { min-height: 100dvh; }
.hero { min-height: 100svh; }
```

`dvh` reflows while the URL bar collapses, which shifts marketing content mid-scroll;
that is why heroes get `svh`. Keep a `100vh` line before the modern unit only when the
support matrix includes browsers without it.

## Safe areas

```css
.app-header {
  padding-top: calc(var(--space-3, 0.75rem) + env(safe-area-inset-top, 0px));
}
.tab-bar {
  padding-bottom: calc(var(--space-2, 0.5rem) + env(safe-area-inset-bottom, 0px));
  padding-inline: max(var(--space-4, 1rem), env(safe-area-inset-left, 0px))
                  max(var(--space-4, 1rem), env(safe-area-inset-right, 0px));
}
.sheet {
  padding-bottom: calc(var(--space-4, 1rem) + env(safe-area-inset-bottom, 0px));
}
```

- Insets are `0` unless the viewport has `viewport-fit=cover`.
- Fixed and sticky chrome needs insets; normal flow content usually inherits clearance
  from the header and footer. Pad the underlying content too, so it can scroll fully
  past a fixed bottom bar.
- Use `max()` horizontally in landscape so the inset replaces the margin rather than
  stacking on it; use `calc()` vertically when the control needs both.
- A translucent bar's background should continue under the home indicator while its
  controls sit above it.

## Gesture ownership

`touch-action` lists what the browser may still do on an element:

| Surface | `touch-action` | Browser keeps |
| --- | --- | --- |
| Native horizontal scroller or snap carousel | default (`auto`) | All panning |
| JavaScript horizontal drag (swipe row, slider, custom carousel) | `pan-y pinch-zoom` | Vertical scroll, zoom |
| Vertical drag handle (sheet grabber) | `pan-x pinch-zoom` | Horizontal pan, zoom |
| Canvas or map that handles every axis | `none` | Nothing; use only on the surface itself |
| Ordinary tappable controls | `manipulation` | Panning and pinch zoom |

The effective value intersects with every ancestor's, so a permissive child cannot
loosen a restrictive parent. Keep sheet content scrollable; restrict the handle, not
the whole sheet.

```css
.carousel-track {
  display: flex;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  overscroll-behavior-x: contain;
}
.carousel-track > * { scroll-snap-align: start; }
```

Prefer this native track over a JavaScript carousel: browser scroll physics beat a
hand-rolled spring. Use `proximity` instead of `mandatory` when a slide can be taller
or wider than the viewport, or its content becomes unreachable.

```js
handle.addEventListener('pointerdown', (e) => {
  handle.setPointerCapture(e.pointerId); // keep tracking outside the element
});
handle.addEventListener('pointercancel', reset); // browser took over: no action, no stuck state
```

Thresholds, velocity, projection, rubber-banding, and spring handoff are in
[gestures](../better-animation/gestures.md). Keep a button or keyboard path for
every drag-only action.

## Overscroll

```css
.sheet-content, .chat-log {
  overflow-y: auto;
  overscroll-behavior-y: contain; /* own bounce stays; the page behind does not move */
}

/* App-like shells only, where pull-to-refresh or page bounce conflicts */
html { overscroll-behavior-y: none; }
```

Use `contain` to keep local overscroll effects, `none` where those also conflict.
Limit suppression to the conflicting axis so browsers that navigate history with
horizontal overscroll keep doing so. Scrolling documents keep pull-to-refresh. Never
use a non-passive `touchmove` listener with `preventDefault()`: it blocks scrolling
and costs frames.
Remove obsolete `-webkit-overflow-scrolling: touch`; momentum scrolling is default.

Overscroll containment does not make a modal. Background scroll lock, focus trap,
and focus restoration come from the project's dialog or sheet primitive.

## Browser chrome color

```html
<meta name="theme-color" media="(prefers-color-scheme: light)" content="#ffffff" />
<meta name="theme-color" media="(prefers-color-scheme: dark)" content="#0a0a0a" />
<meta name="color-scheme" content="light dark" />
```

Match each value to the color at the very top of the page (the header background),
not the brand color. For an in-app theme toggle, update the active tag's `content`
when the theme changes. Some browsers ignore `theme-color` and sample the page's top
background or fixed header instead, so keep that surface and `body` consistent with
the tags. Declare `color-scheme` only for schemes the interface actually styles.

## Installed mode

```json
{
  "display": "standalone",
  "theme_color": "#ffffff",
  "background_color": "#ffffff"
}
```

```css
@media (display-mode: standalone) {
  .back-button { display: inline-flex; } /* no browser back button here */
}
```

- `background_color` paints the launch screen; match the first painted surface to
  avoid a flash.
- `apple-mobile-web-app-status-bar-style` takes keywords (`default`, `black`,
  `black-translucent`), not colors; `black-translucent` draws content under the status
  bar, so it needs top safe-area padding.
- Standalone removes the URL bar, back button, share, and reload. Every screen needs an
  in-app way back; check where links outside the app's scope open.
- Installability, service workers, and offline behavior are separate work; they need a
  secure context (see [baseline-and-testing.md](baseline-and-testing.md#on-a-device)).
