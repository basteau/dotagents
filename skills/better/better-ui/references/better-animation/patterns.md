# Interaction patterns

These specify behavior rather than a framework. Implement them through the target
platform's component primitives, gesture recognizers, presentation lifecycle, and
animation facilities. Keep existing focus, dismissal, and navigation behavior.
Values are starting points where the project has no motion convention; curves and
durations come from [timing-and-performance.md](timing-and-performance.md). Exact web
code is in [recipes.md](recipes.md), presence and exit lifecycles in
[enter-exit.md](enter-exit.md), icon swaps and morphs in
[icons-and-morphing.md](icons-and-morphing.md), and drag in [gestures.md](gestures.md).

## Press feedback

For a control that benefits from physical feedback, scale it to about 97% of rest
over 100–160ms; keep an established nearby value such as 96%. Respond on press-down,
not on release, and let a cancelled press (dragged away, interrupted) return without
activating. Scaling the visual must not shrink the hit area. Not every control needs
it; give the component an opt-out where motion distracts, and never scale a
disabled control. Under reduced motion, keep scale unchanged and use color, surface,
or label feedback. Hover styling is optional and only for hover-capable input.

## Popover, menu, tooltip, dialog

| Surface | Initial state | Settled state | Starting timing |
| --- | --- | --- | --- |
| Anchored popover or menu | 95% scale, transparent, origin at trigger | Full size and opacity | 150–200ms ease-out |
| Tooltip | 97% scale, transparent, origin at trigger | Full size and opacity | 125ms ease-out |
| Unanchored dialog | 96% scale, transparent, centered | Full size and opacity | 200–250ms ease-out |

Never enter from zero scale: nothing real appears from nothing. Use the primitive's
anchor geometry so the origin follows the trigger when placement flips; dialogs are
the exception and stay centered. Animate a dialog's backdrop (a native `::backdrop`
or an overlay element) with its surface, same curve and duration, so the two read as
one; dim it to direct focus, and exit both together and faster than they entered.
Native `<dialog>` and popovers leave `display: none`, so they need starting styles
and discrete transitions: [recipes.md](recipes.md#entering-from-display-none-native-dialog-and-popover). A plain fade is valid when movement adds
nothing. Under reduced motion, drop the scale and keep an optional short fade.

For pointer tooltips, a first-hover delay prevents accidental activation. Once one
is open, neighbours open with no delay and no animation, which makes a toolbar feel
fast. Tooltips must also open on focus and never be the only route to information.

## Drawers and sheets

Translate a sheet by its own size (a percentage of itself), not a fixed pixel
distance, so content height never matters. Use the drawer curve, or a spring with no
visible overshoot unless a flick preceded it. Exit along the entry path. Check safe
areas, shadows, oversized content, and that a closed sheet is truly hidden, not just
displaced. Dragging is a gesture problem: see [gestures.md](gestures.md).

## Toasts and notifications

Toasts arrive in bursts, so use retargeting transitions, never restarting keyframes.
Enter and exit from the same edge. Toast motion may be slightly slower and softer
than generic UI when it matches the component's personality; cohesion with the
design matters more than the generic budget. In a stack, the opacity change has to
work with the height change; there is no formula, so tune it and look again the next
day. Handle the invisible edge cases: pause timers while the page is hidden or the
stack is hovered, keep hover alive across gaps between stacked items, and keep
dismissed items out of focus order. Announce content through a live region, not
through the animation.

## Accordions and container size

A size change needs real layout change; do not scale text to fake reflow. Use the
platform's layout animation, or measure and animate:

1. **Two elements, never one.** An outer element animates its size; an inner element
   holds the content and is measured. Measuring the element you animate creates a
   feedback loop.
2. **Observe, do not poll.** Measure with a resize observer attached once the node
   exists; never read layout on every render or frame.
3. **Guard the first frame.** A zero measurement before layout must fall back to
   the natural size, or the container grows from nothing on mount.
4. **Clip during the transition** (`overflow: hidden` on the outer element), and keep
   collapsed content out of focus order.
5. **Let the container trail.** A short delay (about 50ms) on the size change reads
   as the container catching up with its content.

Start around 200ms; size animation costs layout every frame, so keep it short.
Reserve it for interactive size changes (accordions, FAQ rows, a button whose label
changes to a loading state), not every container on the page. Test changing
content, text scaling, rapid reversal, and resizing. Code:
[recipes.md](recipes.md#accordion-and-animated-bounds).

## Tabs, segmented controls, indicators

Move a single indicator between options as a shared element; it should glide, not
fade out and back in. When text and background colors must change in perfect sync,
clip a styled duplicate of the strip instead of timing individual color
transitions. The duplicate is decorative: hide it from assistive technology, keep it
out of focus, and stop it intercepting input. Do not imply hierarchy between peer
tabs with directional slides unless the product intends it. Content panels for
high-frequency tab switching change instantly.

## Stagger and reveals

Stagger only a small, infrequent group where sequence explains hierarchy: a hero,
an empty state, a first load. Gap 30–80ms per item, or about 100ms between a few
semantic chunks (title, description, actions). Cap the total so the last item never
waits noticeably. Stagger is decorative: never block interaction, and never let
focus land on a control that is still invisible. Do not stagger routine lists,
hovers, or recycled rows that return to the viewport.

Scroll reveals belong on marketing and explanatory pages, not daily tools. Reveal
once, keep content visible if setup fails, and under reduced motion show the final
state without movement or stagger.

## Crossfades and shared elements

Tune the overlap before adding effects. When two states visibly double-expose and
no curve fixes it, a brief blur of a few pixels blends them into one perceived
change; keep it small, since blur is expensive, and remove it if it harms text
clarity or frame time. A shared element keeps its identity and follows a coherent
path between its two states. Keep semantic state independent of decorative copies.

View transitions follow the same timing rules: a root crossfade around 150–250ms
with a gentle ease, a shared element moving between positions with ease-in-out,
around 250–350ms, using the project's tokens. Keep them short: the snapshot layer
covers live content and intercepts pointer input until it finishes, and a new
transition skips the running one, so nothing a person may reverse mid-flight
belongs there. Under
reduced motion, drop group movement and resizing and keep at most the crossfade.
Naming, cleanup, and styling mechanics are in
[better-polish/pseudo-elements.md](../better-polish/pseudo-elements.md).

## Number and text changes

Changing numbers use tabular figures so width does not jitter. Animate digits only
for occasional, meaningful changes (a total updating, a counter on success), not
live tickers or values someone is reading; roll in the direction of change. Keep
the accessible value correct throughout and announce updates through a live region
at a sane rate, never per frame. Per-character text morphs suit rare label changes
such as "Copy" to "Copied", paired with a container size animation when the width
changes. Under reduced motion, swap the value instantly.

## Hold to confirm

For destructive actions where a click is too easy, fill a progress overlay over
about 2s with linear timing while pressed, and snap back in about 200ms on release:
slow where the user decides, fast where the system responds. The fill is styling;
the action, cancellation, and keyboard equivalent are separate logic.

## Navigation and haptics

Keep native navigation transitions, back gestures, and system-surface behavior.
Custom motion communicates the relationship between destinations. Where haptics or
sound exist, fire them on the causal event and on the same frame as the visual
change, never after an animation completes; see [better-sound](../better-sound.md).
Respect device capability and user preference, and keep equivalent visual feedback.
