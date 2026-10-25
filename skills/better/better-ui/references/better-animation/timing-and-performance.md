# Timing and performance

Starting values for curves, durations, springs, and rendering cost. Use the target
project's motion tokens when they serve the interaction; these fill the gaps. Copy
a value from a table rather than approximating a familiar-looking one.

## Pick the timing model first

Ask whether the motion answers the user's hand or the system is speaking.

| Motion | Timing model | Why |
| --- | --- | --- |
| User-driven: drag, flick, swipe, pinch, pointer-following | Spring | Retargets mid-flight and carries release velocity |
| Interruptible toggle the user may reverse mid-motion | Spring, or a retargeting transition | Continues from the current value instead of restarting |
| System-driven: state change, feedback, announcement, entry, exit | Easing curve with a duration | Clear start and end, predictable timing; a spring on a toast feels restless |
| Time representation: progress, countdown, hold fill, marquee | Linear | Progress maps one-to-one to time |
| High-frequency: typing, shortcuts, keyboard navigation, rapid toggles | None | Any animation reads as delay |
| Overshoot-and-settle wanted | Spring | An easing curve cannot settle naturally past its target |

## Easing

| Situation | Curve |
| --- | --- |
| Entering, or responding to the user | Ease-out (fast start, gentle landing) |
| Exiting | Ease-out by default, shorter than the entrance; see below |
| Moving or morphing between two on-screen positions, view or mode change | Ease-in-out |
| Hover color, opacity, or other small in-place change | `ease` or a gentle standard curve |
| Constant motion or elapsed time | Linear |
| Unsure | Ease-out |

Never ease-in on an entrance or anything the user waits to see respond: it moves
slowest at the moment they are watching. Ease-out at 200ms feels faster than ease-in
at 200ms. An accelerating exit is acceptable only for a short departure fully off
screen (at most about 200ms) that nothing waits on. Linear on spatial movement feels
mechanical; keep it for time.

Built-in CSS keywords are weak for deliberate motion. Strong starting curves:

| Token | Cubic Bézier `(x1, y1, x2, y2)` | Use |
| --- | --- | --- |
| `--ease-out` | `(0.23, 1, 0.32, 1)` | Entrances, responses, most UI |
| `--ease-in-out` | `(0.77, 0, 0.175, 1)` | On-screen movement and morphs |
| `--ease-drawer` | `(0.32, 0.72, 0, 1)` | iOS-like drawers and sheets |

These describe timing, not an API; map them to the platform's curve type or an
existing token. Take further curves from an established easing library rather than
hand-rolling control points. If motion feels slow, shorten the duration before
sharpening the curve.

## Duration

| Interaction | Curve | Starting duration |
| --- | --- | --- |
| Press feedback (scale 0.97) | Ease-out | 100–160ms |
| Hover color or opacity | `ease` | 120–180ms |
| Small state change: switch, checkbox, toggle | Ease-out or `ease` | 180–260ms |
| Tooltip or small popover | Ease-out | 125–200ms |
| Dropdown, select, anchored menu | Ease-out | 150–250ms |
| Context menu | None on entry | Instant in; optional ~100–150ms fade out |
| Centered dialog | Ease-out | 200–300ms in; ~150ms out |
| Toast | Ease-out, or `ease` for a softer brand | 200–400ms |
| Drawer or sheet | Drawer curve | 200–500ms, tuned to travel distance |
| On-screen move, page or view transition | Ease-in-out | 200–300ms (250ms is a good start) |
| Hold-to-confirm | Linear fill, ease-out release | ~2s hold; 200ms snap back |
| Stagger between items | Ease-out per item | 30–50ms; up to 80ms for a few items |
| Spinner, progress, marquee | Linear | Match the actual process |
| Marketing or explanatory sequence | Any that serves it | Can be longer |

Keep user-initiated UI motion under 300ms. A 180ms select feels more responsive than
a 400ms one. Larger surfaces, native navigation, and explanatory sequences can
justify more; built-in platform timing is not a finding by itself. Similar elements
share identical timing: two buttons at 150ms and 200ms read as a mistake.

Time the phases separately when the user decides. A deliberate phase (hold, destructive
confirm) can be slow; the system's response always snaps. Animation styling does not
implement confirmation, cancellation, or keyboard access.

Perceived speed is part of the budget: a faster spinner makes the same wait feel
shorter, and tooltips after the first open can skip both delay and animation.

## Springs

Keep ordinary controls critically damped or nearly so. Visible bounce (0.1–0.3)
belongs to drag release, dismissal, and deliberately playful moments.

| Use | Motion (`motion.dev`) starting config | Feel |
| --- | --- | --- |
| Drag release, snap back, swipe settle | `stiffness: 500, damping: 30` | Firm, settles quickly |
| Interruptible position toggle | `stiffness: 400, damping: 25` | Responsive, slight give |
| Element that should feel alive | `duration: 0.5, bounce: 0.2` | Easy to reason about; one setting per feel |
| Decorative pointer or scroll tracking | `stiffness: 100, damping: 10, mass: 1` | Soft and visibly springy; decorative only |
| No overshoot allowed | `bounce: 0`, or damping ratio 1 | Settles without oscillation |

Spring parameters are not portable. Engines differ in units and precedence for mass,
stiffness, damping, damping ratio, response, duration, and bounce. For a
physics-style spring, damping ratio ≈ `damping / (2 × √(stiffness × mass))`: 1 settles
without overshoot, lower bounces. Check the trajectory in the target engine, avoid
very high stiffness with very low damping, and clamp where a boundary must never be
crossed. Pass gesture release velocity into the spring so a fast flick travels
faster than a slow one. Tying a value directly to the pointer feels artificial;
smoothing it through a spring adds momentum, but only for decoration, never for data.

## Rendering cost

| Tier | Web properties | Cost per frame |
| --- | --- | --- |
| Composite only | `transform`, `opacity`; increasingly `filter`, `clip-path`, `background-color` | Cheapest; can run off the main thread |
| Paint + composite | `box-shadow`, `border-radius`, `color` | Redraw every frame |
| Layout + paint + composite | `width`, `height`, `padding`, `margin`, `top`, `left`, `border-width` | Re-measures and can move everything around it |

| Instead of animating | Animate |
| --- | --- |
| `width`, `height`, `padding` to grow or shrink | `scale()` (it scales children too) |
| `top`, `left`, `margin` to move | `translate()`; percentages are relative to the element itself |
| `display` or `visibility` swaps | `opacity` with retained presentation |
| `box-shadow` | `filter: drop-shadow()`, or crossfade a pre-rendered shadow layer |

Layout animation is justified only when layout must change, as in an accordion;
measure it. A layout property that looks fine on a fast machine can still regress on
a slow one, and a sidebar animating `margin-left` re-lays out the page beside it.
Support for the newer composite properties is uneven; test across engines. A small
color transition can be fine even though it repaints.

Name the properties. `transition: all` animates whatever changes next, including
layout properties and colors nobody meant to animate. In Tailwind, prefer
`transition-[scale,opacity]`; `transition-transform` covers `transform`, `translate`,
`scale`, and `rotate`.

### Where it runs

A JS loop on `requestAnimationFrame` competes with everything on the main thread.
CSS transitions, keyframes, and the Web Animations API can run off it. Use CSS or
WAAPI for predetermined motion that plays while the page is busy (route changes,
hydration, data fetching); use a JS engine for springs, gestures, layout and exit
animation. Acceleration is something the browser may grant, not a guarantee. On
native platforms, the same split applies to the UI thread and the platform's
animation driver; check the runtime's documentation rather than assuming.

Traps that look correct:

- **Rendering per frame.** A framework state update inside a pointer, scroll, or
  frame callback re-renders every frame. Write to the element's style or a motion value.
- **Inherited CSS variables.** Driving a child's `transform` through a custom
  property on a parent recalculates styles for every descendant; it slows as content
  grows. Set `transform` on the animating element.
- **Blur.** Keep animated `blur()` under 20px, especially for Safari; 2–5px is
  enough to soften a crossfade.
- **Motion shorthands (optional, Motion for React).** `x`, `y`, `scale` props are
  implemented through CSS variables and stay on the main thread. When the animation
  must survive a busy page, animate a full string: `transform: "translateX(100px)"`.

### Layer hints

`will-change` pre-promotes an element so a mid-animation CPU-to-GPU hand-off does not
shift it by a pixel or stutter on the first frame (Safari benefits most). Add it only
after seeing that shift or measuring dropped frames, on the animating element, for
`transform`, `opacity`, or `filter`. Never `will-change: all`, never on `*` or large
containers, never for properties that cannot composite. Every layer costs memory;
avoid permanent layers across a whole list.

### Frame budget and diagnosis

Budget roughly 16.7ms per frame at 60Hz and 8.3ms at 120Hz; design for the smaller.
A fast development laptop hides most problems: verify on a mid-range phone or a
representative release build. Desktop CPU throttling does not model a weak GPU.

Diagnose in order of likelihood, and name the cause before changing code:

1. Which properties animate? Layout first, then shadow and radius.
2. Does jank coincide with a busy main thread? Move to CSS or WAAPI.
3. Is the framework rendering per frame?
4. Does it worsen as content grows? Inherited variable or too many animating nodes.
5. Is a CSS variable animated, directly or through a library shorthand?
6. Any animated blur above 20px?
7. Still shifting at start or end? Only now add `will-change`.

Confirm with a performance recording and the animation inspector, not by feel.
Report each finding with its cause, the before and after, and whether it was
verified from code or needs a device recording.
