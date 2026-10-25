# Icon transitions and morphing

Two ways an icon can change state: a contextual crossfade between two drawings, or a
morph where the strokes themselves move. Icon weight, color, sizing, and direction
belong to [better-polish/icons.md](../better-polish/icons.md).

## Which icons animate

| Animate | Keep instant |
| --- | --- |
| Infrequent state swaps: play to pause, like to liked, copy to copied | Hover-revealed actions in list rows |
| Icons in a toolbar that appears on selection | Tab and navigation icon swaps |
| Loading and success indicators | Decorative or always-visible icons |
| Menu to close, plus to cross, chevron direction | The text label beside an icon |

An icon change is never the only cue: the label, accessible name, or pressed state
must change too. Decorative icon SVGs are hidden from assistive technology.

## Contextual crossfade

Animate the swap with opacity, scale, and blur together rather than toggling
visibility. Where the project has no icon-swap convention, use this set as a unit:

| Property | Hidden | Shown |
| --- | --- | --- |
| Scale | `0.25` | `1` |
| Opacity | `0` | `1` |
| Blur | `4px` | `0px` |
| Timing | `300ms cubic-bezier(0.2, 0, 0, 1)`, or a critically damped spring of similar length | |

The small starting scale makes the new icon grow out of the old one's center; a
half-way value such as `0.5` reads as a resize instead. Keep bounce at zero: an icon
that overshoots on every swap draws attention to the chrome. Skip the animation on
first render so the default icon does not animate in. Under reduced motion, keep only
the opacity fade.

Use the CSS version by default; never add a dependency for an icon swap. In a
project already on Motion, match its import path (`motion/react` or
`framer-motion`) and use the optional version below.

### CSS, both icons mounted

Both icons share one grid cell, so enter and exit both animate and the larger icon
sets the size. Transitions do not run on first render, so the default icon never
animates in.

```html
<button aria-pressed="false" aria-label="Like">
  <span class="icon-swap" aria-hidden="true">
    <svg class="icon-layer" data-icon="on">…</svg>
    <svg class="icon-layer" data-icon="off">…</svg>
  </span>
</button>
```
```css
.icon-swap { display: grid; place-items: center; }
.icon-layer {
  grid-area: 1 / 1;
  transition: opacity 300ms, scale 300ms, filter 300ms;
  transition-timing-function: cubic-bezier(0.2, 0, 0, 1);
}
[aria-pressed="true"] [data-icon="off"],
[aria-pressed="false"] [data-icon="on"] { opacity: 0; scale: 0.25; filter: blur(4px); }
@media (prefers-reduced-motion: reduce) {
  .icon-swap .icon-layer[data-icon] { scale: 1; filter: none; } /* fade only */
}
```

### Optional: Motion

```tsx
<AnimatePresence initial={false} mode="popLayout">
  <motion.span
    key={isActive ? "active" : "inactive"}
    initial={{ opacity: 0, scale: 0.25, filter: "blur(4px)" }}
    animate={{ opacity: 1, scale: 1, filter: "blur(0px)" }}
    exit={{ opacity: 0, scale: 0.25, filter: "blur(4px)" }}
    transition={{ type: "spring", duration: 0.3, bounce: 0 }}
  >
    {isActive ? <ActiveIcon /> : <InactiveIcon />}
  </motion.span>
</AnimatePresence>
```

## Morphing icons

A morph moves the strokes from one shape to another, so menu becomes close and plus
becomes cross as one object rather than two drawings swapping. It works when every
icon in the set shares the same structure, so any icon can morph into any other.

- **Fixed stroke count.** Every icon is exactly three lines (or one fixed number
  across the set). No icon has more; icons that need fewer collapse the rest.
- **Collapse, never omit.** An unused line is a zero-length line at the center with
  opacity 0, so it shrinks to and grows from a point. Missing or null entries break
  the interpolation.
- **One coordinate space.** Every icon uses the same viewBox (14 by 14 is a good
  default, center 7) so coordinates interpolate directly.
- **Points are lines too.** Dot icons (more, grip) are zero-length lines with round
  caps, which lets dots stretch into strokes.
- **Rotation groups.** Rotational variants (arrow directions, plus and cross) share
  the same base lines and a group; only rotation differs. Within a group, animate
  rotation with a spring; across groups, jump rotation instantly and let the lines
  morph, or the icon spins on an unrelated change.
- **Round line caps** on every line, so endpoints stay soft while they travel.
- **Timing.** A strong exponential ease-out, about 400ms
  (`cubic-bezier(0.19, 1, 0.22, 1)`), for the line coordinates.
- **Reduced motion** sets the duration to zero; the icon still changes.

```ts
type Line = { x1: number; y1: number; x2: number; y2: number; opacity?: number };
type IconDef = { lines: [Line, Line, Line]; rotation?: number; group?: string };

const C = 7; // center of a 14x14 viewBox
const collapsed: Line = { x1: C, y1: C, x2: C, y2: C, opacity: 0 };

const plus: IconDef["lines"] = [{ x1: 2, y1: 7, x2: 12, y2: 7 }, { x1: 7, y1: 2, x2: 7, y2: 12 }, collapsed];
const arrow: IconDef["lines"] = [
  { x1: 2, y1: 7, x2: 12, y2: 7 },
  { x1: 7.5, y1: 2.5, x2: 12, y2: 7 },
  { x1: 7.5, y1: 11.5, x2: 12, y2: 7 },
];

const icons: Record<string, IconDef> = {
  plus:          { lines: plus, rotation: 0, group: "plus" },
  close:         { lines: plus, rotation: 45, group: "plus" },
  "arrow-right": { lines: arrow, rotation: 0, group: "arrow" },
  "arrow-down":  { lines: arrow, rotation: 90, group: "arrow" },
  minus:         { lines: [{ x1: 2, y1: 7, x2: 12, y2: 7 }, collapsed, collapsed] },
};
```

Without a library, interpolate the coordinates with the platform's animation API
or a frame loop, since line endpoints are attributes rather than animatable CSS.

### Optional: Motion morph

```tsx
function MorphIcon({ name, size = 16 }) {
  const def = icons[name];
  const reduce = useReducedMotion() ?? false;
  const transition = reduce ? { duration: 0 } : { ease: [0.19, 1, 0.22, 1], duration: 0.4 };
  const rotation = useSpring(def.rotation ?? 0, { bounce: 0, duration: 0.4 });
  const prevGroup = useRef(def.group);

  useEffect(() => {
    const sameGroup = def.group && def.group === prevGroup.current;
    if (sameGroup && !reduce) rotation.set(def.rotation ?? 0); // spring within a group
    else rotation.jump(def.rotation ?? 0); // instant across groups
    prevGroup.current = def.group;
  }, [def, reduce, rotation]);

  return (
    <svg viewBox="0 0 14 14" width={size} height={size} aria-hidden="true">
      <motion.g style={{ rotate: rotation, transformOrigin: "center" }}>
        {def.lines.map((l, i) => (
          <motion.line key={i} initial={false} animate={{ ...l, opacity: l.opacity ?? 1 }}
            transition={transition} stroke="currentColor" strokeWidth={1.5} strokeLinecap="round" />
        ))}
      </motion.g>
    </svg>
  );
}
```
