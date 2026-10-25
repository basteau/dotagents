# Gestures and fluid motion

An interface feels alive when motion starts from what is on screen now, inherits the
person's velocity, projects momentum forward, and can be grabbed and reversed at any
instant. Use the platform's gesture system and springs; the web examples use Pointer
Events and plain math. General spring settings live in
[timing-and-performance.md](timing-and-performance.md#springs).

## Respond on contact

Show feedback on press-down, not release; the moment latency appears, directness
collapses. Audit everything on the input path: debounces, artificial timers,
transition waits, the legacy tap delay. During a drag, slider, or sheet, update
continuously with the pointer the whole way, never only when the gesture ends.

## Track one to one

Touch and content move together. Keep the offset from where the element was grabbed;
snapping its center to the pointer breaks the illusion. Capture the pointer once the
drag starts so tracking continues outside the element's bounds. Ignore additional
contacts once a drag has begun, or switching fingers makes the element jump. Keep a
short history of recent positions and timestamps; release velocity comes from it.

Set the transform on the dragged element itself. Driving it through a variable on a
parent recalculates styles for every descendant on every frame.

## Never lock out interruption

The thought and the gesture happen in parallel. A person must be able to grab a
moving element mid-flight and reverse it; a closing sheet that is grabbed again
follows the finger instead of finishing first.

- **Start from the presentation value.** On interrupt, read the live on-screen
  position and animate from there. Starting from the logical target causes a jump.
- **Carry velocity through a retarget.** Replacing one animation with another at a
  reversal creates a velocity discontinuity, a brick wall. Springs that retarget
  from current velocity avoid it.
- **Keep gesture motion off fixed timelines.** CSS transitions and keyframes cannot
  be grabbed and thrown; use a spring or the platform's interactive animator.
- **Decompose 2D motion** into independent horizontal and vertical springs. One
  spring on a 2D distance desyncs when the axes have different velocities.

## Springs: damping and response

Think in two parameters: damping ratio (1 settles without overshoot, below 1
overshoots; lower is bouncier) and response (how quickly the value arrives, not a
duration; a spring's settle time emerges from its parameters).

| Interaction | Damping ratio | Response |
| --- | --- | --- |
| Default UI, reposition (picture-in-picture) | `1.0` | `0.4s` |
| Rotation | `0.8` | `0.4s` |
| Drawer or sheet after a drag | `0.8` | `0.3s` |

Default to critical damping. Add bounce only when the gesture itself carried
momentum: overshoot on a flicked card feels right; on a menu that faded in it feels
wrong. In Motion, `bounce` plus `duration` maps closely to damping plus response.

## Hand off velocity at release

When the finger lifts, the animation continues at the finger's exact velocity, so
there is no seam between dragging and animating. This is the detail that most
separates fluid from fine. APIs that take absolute velocity (Motion's `velocity`, in
px/s) get the raw value. APIs that want relative velocity normalize by the remaining
distance:

```js
relativeVelocity = releaseVelocity / (target - current);
// at y=50, target y=150, finger at 50px/s: 50 / 100 = 0.5
```

## Project momentum, then snap

Do not pick a snap point from where the finger lifted. Project where the motion is
going, the way scroll deceleration does, then choose the snap point nearest the
projection and hand off velocity to reach it. A small flick produces a big result.

```js
// decelerationRate ~0.998 for normal scroll feel, 0.99 for snappier
function project(velocityPxPerSec, decelerationRate = 0.998) {
  return ((velocityPxPerSec / 1000) * decelerationRate) / (1 - decelerationRate);
}
const target = nearestSnapPoint(position + project(velocity));
```

Use this exponential-decay form rather than a textbook constant-deceleration
formula. Decide commit versus return from the projection and the velocity's
direction, not from position alone.

## Dismiss on distance or flick

A drag-to-dismiss commits when it passes a distance threshold or when release
velocity is high enough, whichever comes first; a quick flick should be enough.
Measure velocity over the last few samples, not the whole gesture: a late flick
counts, a slow release after a fast start does not. Keep units explicit (around
`0.11` px/ms is a starting threshold for a toast or sheet) and respect direction: a
flick back toward rest cancels. Settle a cancelled drag back with a spring that
receives the release velocity.

## Rubber-band at boundaries

At an edge, resist progressively instead of stopping hard. A hard stop reads as
frozen; continuous resistance reads as "responsive, but nothing more here". Allow
the over-drag with rising friction, then spring back from the current position.

```js
// The further past the bound, the less the element follows
function rubberband(overshoot, dimension, constant = 0.55) {
  return (overshoot * dimension * constant) / (dimension + constant * Math.abs(overshoot));
}
```

## Disambiguate quickly

- **Hysteresis.** Require about 10px of movement before committing to a direction,
  then track one to one. Give taps a similar tolerance, highlight on down, commit on
  up, and allow cancelling by dragging away and back.
- **Recognize in parallel.** Detect all plausible gestures from the first move and
  cancel the losers once intent is clear. Avoid recognizers that only report a final
  state; they discard the continuous tracking feedback needs.
- **Pay for double-tap only where it exists**; its detection delays every single tap.
- **Yield to the platform.** Resolve conflicts with scroll, back-swipe, and
  pull-to-refresh using the platform's gesture ownership (on the web, `touch-action`
  on the dragged axis). Handle cancellation and release outside bounds.

## Hint where it is going

People predict the end state from the trajectory, so make intermediate frames point
at the outcome: a panel grows up and out toward the finger, a card tilts toward its
destination. Keep enter and exit on the same path, and mirror the easing on
reversible transitions so the return retraces the way out.

## Keep frames clean

Smoothness is about what is in the frames, not only the frame rate. Animate
transforms and opacity on the display-synced clock; keep per-frame displacement
below the point where motion strobes. For very fast travel, a subtle stretch or
motion blur encodes speed better than a sharp streak. Fade large surfaces during a
large reposition and restore them when settled.

## Feedback across senses

Sound and haptics follow three rules: causality (fire on the actual event, such as
the snap home), harmony (same frame as the visual; no lag between them), and utility
(only for meaningful moments: commit, snap, success, error). Over-feedback trains
people to ignore all of it. See [better-sound](../better-sound.md).

## Access and verification

Every gesture needs a non-drag equivalent: a button, a keyboard action, or the
component's existing control. Under reduced motion, direct tracking stays (the
person drives it), but settle without overshoot, drop projected flings, parallax,
and decorative stretch. Test reversal mid-flight, a second contact, scroll conflicts,
slow and fast releases, and the actual target device; a desktop pointer cannot tell
you how a flick feels. See [better-pwa](../better-pwa.md) for touch surfaces in
installed web apps.

## Optional: Motion

Motion's drag carries release velocity into its return animation and applies
elasticity past constraints; decide dismissal from the projection.

```tsx
<motion.div
  drag="y"
  dragConstraints={{ top: 0, bottom: 0 }}
  dragElastic={{ top: 0.1, bottom: 1 }} // resist upward, follow downward
  onDragEnd={(_, { offset, velocity }) => {
    if (offset.y + project(velocity.y) > sheetHeight / 2) close(velocity.y); // pass velocity on
  }}
/>
```
