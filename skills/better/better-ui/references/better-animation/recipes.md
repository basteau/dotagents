# Web motion recipes

Exact implementations for web projects, to start from rather than rebuild. Values
are recipe-specific starting points: keep the project's motion tokens, component
primitives, and styling system (Tailwind in a Tailwind project, the existing CSS
approach otherwise). The behavior behind each recipe is in [patterns.md](patterns.md);
the `--ease-*` curves are defined in [timing-and-performance.md](timing-and-performance.md).
Attribute hooks such as `data-starting-style` are Base UI's; use the primitive's own.

## Transitions for state, keyframes for sequences

Transitions retarget mid-flight; keyframes restart. Never put `animation:` on
`:hover`, `.open`, or another toggled state.

```css
.drawer { transform: translateX(-100%); transition: transform 200ms var(--ease-out); }
.drawer.open { transform: translateX(0); }          /* good: reverses mid-flight */
.drawer.open { animation: slideIn 200ms forwards; } /* bad: snaps when interrupted */
```

## Button press

```css
.button { transition: scale 150ms var(--ease-out); }
.button:not(:disabled):active { scale: 0.97; }
```

`:active` is a real press on touch, so it needs no hover gate. Tailwind:
`transition-transform duration-150 ease-out enabled:active:scale-[0.97]`; Motion:
`whileTap={{ scale: 0.97 }}` unless disabled. Opt out through a variant or `static` prop.

## Popover, menu, tooltip, dialog

```css
.popover {
  transform-origin: var(--transform-origin); /* from the positioning primitive */
  transition: opacity 200ms var(--ease-out), transform 200ms var(--ease-out);
}
.popover[data-starting-style], .popover[data-ending-style] { opacity: 0; transform: scale(0.95); }
.tooltip { transition-duration: 125ms; } /* tooltip and dialog extend .popover */
.tooltip[data-starting-style], .tooltip[data-ending-style] { transform: scale(0.97); }
.tooltip[data-instant] { transition-duration: 0ms; } /* neighbours after the first */
.dialog { transform-origin: center; transition-duration: 250ms; } /* not anchored */
.dialog[data-starting-style], .dialog[data-ending-style] { transform: scale(0.96); }
.backdrop { transition: opacity 250ms var(--ease-out); } /* with the surface */
```

## Entering from display none: native dialog and popover

```css
dialog, dialog::backdrop {
  opacity: 0;
  transition-property: opacity, scale, display, overlay;
  transition-duration: 150ms; /* exit, shorter */
  transition-timing-function: var(--ease-out);
  transition-behavior: allow-discrete;
}
dialog { scale: 0.96; }  dialog[open] { scale: 1; }
dialog[open], dialog[open]::backdrop { opacity: 1; transition-duration: 250ms; }
@starting-style {
  dialog[open] { scale: 0.96; }
  dialog[open], dialog[open]::backdrop { opacity: 0; }
}
```

A `[popover]` works the same with `:popover-open` for `[open]`; anchor its origin to
the invoker. `@starting-style` and discrete `display` are Baseline 2024, `overlay`
Chromium only; elsewhere it shows and hides instantly. Backdrop styling lives in
[better-polish/pseudo-elements.md](../better-polish/pseudo-elements.md).

## Drawer and toast

```css
.drawer { transition: transform 500ms var(--ease-drawer); }
.drawer[data-closed] { transform: translateY(100%); } /* its own height */
.toast {
  transition: opacity 400ms ease, transform 400ms ease; /* softer, by personality */
  @starting-style { opacity: 0; transform: translateY(100%); }
}
```

Without `@starting-style`, style the hidden state until a `data-mounted` flag is set
after first paint. Dragging: [gestures.md](gestures.md#dismiss-on-distance-or-flick).

## Accordion and animated bounds

```css
.content {
  overflow: hidden;
  transition: height 200ms var(--ease-out), opacity 200ms var(--ease-out);
}
```

Measure the height (or let a headless primitive supply it, such as
`--accordion-panel-height`) rather than transitioning to `auto`. Where supported,
`interpolate-size: allow-keywords` lets `height: auto` transition natively; others
snap, which is fine. For size that follows changing content, measure an inner
element and animate an outer one:

```tsx
function useMeasure() {
  const [node, setNode] = useState<HTMLElement | null>(null);
  const [bounds, setBounds] = useState({ width: 0, height: 0 });
  const ref = useCallback((el: HTMLElement | null) => setNode(el), []); // callback ref
  useEffect(() => {
    if (!node) return;
    const ro = new ResizeObserver(([e]) => setBounds(e.contentRect));
    ro.observe(node);
    return () => ro.disconnect();
  }, [node]);
  return [ref, bounds] as const;
}
function AnimatedHeight({ children }) {
  const [ref, { height }] = useMeasure();
  return (
    <motion.div
      animate={{ height: height > 0 ? height : "auto" }} // guard the first frame
      transition={{ duration: 0.2, delay: 0.05 }}
      style={{ overflow: "hidden" }}
    >
      <div ref={ref}>{children}</div>
    </motion.div>
  );
}
```

## Staggered entrance

The hidden state lives in the `from` keyframe with `both` fill, so an item whose
animation never runs stays visible. Reduced motion keeps only the fade.

```css
.stagger-item { animation: fade-in 300ms var(--ease-out) both; }
@media (prefers-reduced-motion: no-preference) {
  .stagger-item { animation-name: fade-in-up; }
  .stagger-item:nth-child(2) { animation-delay: 50ms; }
  .stagger-item:nth-child(3) { animation-delay: 100ms; }
}
@keyframes fade-in { from { opacity: 0; } }
@keyframes fade-in-up { from { opacity: 0; transform: translateY(8px); filter: blur(4px); } }
```

For a few semantic chunks of a hero, widen the gap to about 100ms with a 12px rise.

## Hold to confirm

```css
.overlay { clip-path: inset(0 100% 0 0); transition: clip-path 200ms var(--ease-out); }
.button:active .overlay { clip-path: inset(0); transition: clip-path 2s linear; }
```

`linear` is right: the fill is progress. Fire the action from press logic, not styling.

## Tab color change by clipping

Duplicate the strip, style the copy as active, clip it to the active tab's measured
insets, and give it `aria-hidden`, `inert`, and `pointer-events: none`:

```css
.tabs-active-copy {
  clip-path: inset(0 var(--clip-right) 0 var(--clip-left) round 999px);
  transition: clip-path 250ms var(--ease-in-out);
}
```

## Scroll reveal and comparison slider

```css
.reveal { clip-path: inset(0 0 100% 0); transition: clip-path 600ms var(--ease-in-out); }
.reveal[data-visible] { clip-path: inset(0); }
.compare-top { clip-path: inset(0 calc(100% - var(--split)) 0 0); } /* --split from drag */
```

Set `data-visible` once from an `IntersectionObserver` (or `useInView` with
`{ once: true, margin: "-100px" }`), and never remove it. Marketing pages only.

## Crossfade blur mask and programmatic motion

```css
.content { transition: filter 200ms ease, opacity 200ms ease; }
.content.transitioning { filter: blur(2px); opacity: 0.7; }
```
```js
el.animate([{ clipPath: "inset(0 0 100% 0)" }, { clipPath: "inset(0)" }],
  { duration: 600, easing: "cubic-bezier(0.77, 0, 0.175, 1)", fill: "forwards" });
```

WAAPI gives CSS-grade performance and cancellation with no dependency. In Motion,
prefer a full `transform` string over `x`/`y` shorthands when frames drop under load.

## Reduced-motion fallback

```css
.panel { transition: opacity 150ms var(--ease-out); }
@media (prefers-reduced-motion: no-preference) {
  .panel { transition-property: opacity, translate, scale, filter; transition-duration: 250ms; }
}
```

Tailwind: prefix movement, scale, and blur with `motion-safe:`. Motion:
`<MotionConfig reducedMotion="user">` drops transform and layout animation but still
runs `filter`, so branch on `useReducedMotion()` where blur animates.

## Suppress transitions on theme switch

Otherwise every color transition fires at once and the switch smears:

```js
function withoutTransitions(apply) {
  const style = document.createElement("style");
  style.textContent = "*,*::before,*::after{transition:none!important}";
  document.head.append(style);
  apply();                       // flip the theme class or attribute
  void document.body.offsetHeight; // force a style flush while disabled
  requestAnimationFrame(() => requestAnimationFrame(() => style.remove()));
}
```

Call it around an in-app toggle, and from a `prefers-color-scheme` change listener
for OS switches. `next-themes` ships this as `disableTransitionOnChange`.
