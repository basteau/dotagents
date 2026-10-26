# Better animation

Make state changes legible and interactions feel immediate. The bar is motion that
feels right, not motion that merely runs, and restraint is part of it: decide whether
motion earns its attention cost before choosing a curve, then build the smallest
implementation that serves the interaction. Zero lines of motion code is sometimes
the correct result.

This reference owns motion purpose, timing, continuity, gestures, and rendering cost.
[better-polish](better-polish.md) covers static visual finish,
[better-interaction](better-interaction.md) covers loading, feedback flow, perceived
latency, and tooltip and toast timing, [better-sound](better-sound.md) covers audio
feedback, [better-pwa](better-pwa.md) covers hover gating and tap activation, and
[better-accessibility](better-accessibility.md) covers reduced-motion policy and
non-motion state cues; each pattern here applies them. For a cross-domain review,
use [better-ui-review](better-ui-review.md).

## Follow the requested work

- **Build or fix:** implement and verify. A request to improve existing animations
  authorizes scoped fixes, not just a report.
- **Review, audit, or find opportunities:** stay read-only unless fixes are requested;
  use [review-and-planning.md](better-animation/review-and-planning.md).
- **Name an effect:** answer from [vocabulary.md](better-animation/vocabulary.md);
  do not start an implementation.

Read the project's instructions, component code, motion tokens, animation
facilities, supported platforms, and preview or check commands. Keep its stack,
native conventions, primitives, and motion language; extend existing tokens instead
of forking them. Express the intended behavior first, then implement it with the
target platform's APIs. Make the call and give the reason in a line; do not offer
motion options as a menu.

## Decide in order

Run these steps in sequence; the first two can end the work. The reasoning and the
craft bar behind each step live in [principles.md](better-animation/principles.md).

1. Should it animate at all, given how often it is seen?
2. What purpose does it serve?
3. Which mechanism is the cheapest that works?
4. Easing and duration, or a spring?
5. Which properties, from which origin?
6. How does it behave when interrupted, and how does it exit?
7. What do reduced motion, touch, and keyboard get?

## Gate on frequency and purpose

Shortcuts, command palette toggles, keyboard navigation, typing, and context menus
get no entrance animation. Frequent hover and selection feedback stays
near-imperceptible. Occasional menus, dialogs, drawers, and toasts get standard
motion; rare onboarding and success moments hold the delight budget. Activating an
ordinary control by keyboard gets the same feedback as a click.

Name the purpose: feedback, spatial consistency, state indication, preventing a
jarring change, explanation, or delight at the rare tier. If none applies, do not
animate. Data people are reading or acting on does not move for style.

## Choose the cheapest mechanism

| Need | Starting point |
| --- | --- |
| Hover, press, color, reversible state | State-driven transition on named properties |
| Entry without script state | Platform entry styles, such as `@starting-style` on the web |
| Predetermined motion while the page is busy | Declarative animation off the main thread (CSS, WAAPI) |
| Exit, layout, shared elements | Existing component or presentation lifecycle support |
| Gestures, momentum, interruptible values | The platform's gesture system and springs |
| Menus, dialogs, drawers, toasts | An accessible existing component, not a rebuilt one |

Do not add a dependency for a fade.

## Pick timing by who is acting

User-driven motion (drag, flick, pointer-following) uses springs that carry
velocity. System-driven changes use an easing curve with a duration: ease-out for
arrivals and responses, ease-in-out for travel on screen, linear only for time.
Never ease-in on an entrance. Keep routine UI under 300ms, make similar elements
share timing, and time a deliberate phase slower than the system's response. Use
the curves, duration table, and spring starting values in
[timing-and-performance.md](better-animation/timing-and-performance.md).

## Animate cheap properties from the right origin

Prefer transform and opacity; name every animated property and never transition
`all`. Use layout animation only for a genuine layout change, and measure it. Never
enter from `scale(0)`: start from 0.9–0.97 with opacity, or a plain fade when
movement adds nothing. Press feedback uses the project's press scale, or 0.97 where
it has none. Grow anchored surfaces from their trigger and keep dialogs centered.
Rendering cost, frame budgets, and diagnosis live in
[timing-and-performance.md](better-animation/timing-and-performance.md).

## Keep motion continuous

Retarget from the current visual state on reversal; rapidly repeated changes use
transitions or springs, not keyframes that restart. Preserve gesture velocity into
the settle, and clean up interrupted animations and listeners. Exit the way it
entered, usually shorter and subtler, and keep an exiting surface presented without
leaving invisible controls interactive. Stage one focal point at a time and stagger
only small, infrequent groups.

| Topic | Read |
| --- | --- |
| Press, popover, tooltip, dialog, drawer, toast, accordion, stagger, crossfade | [patterns.md](better-animation/patterns.md) |
| Mount, unmount, presence, exit modes, nested exits | [enter-exit.md](better-animation/enter-exit.md) |
| Icon swaps and shape morphs | [icons-and-morphing.md](better-animation/icons-and-morphing.md) |
| Drag, swipe, momentum, rubber-banding | [gestures.md](better-animation/gestures.md) |
| Copy-ready CSS and JavaScript for the patterns above | [recipes.md](better-animation/recipes.md) |

## Ship reduced motion and input gating with it

Honor system and application reduced-motion preferences on every animation path,
script-driven ones included; what to drop and what to keep is
[better-accessibility](better-accessibility/motion-and-zoom.md#what-to-disable-vs-reduce)'s,
and
each pattern's reduced version is in its detail file. Final states, focus, and
feedback must stay correct when motion is off, and completion logic must not wait on
a callback a disabled animation never emits. Gate hover motion as
[better-pwa](better-pwa.md#gate-hover-by-capability-never-by-device) describes, keep
press feedback for touch, and never make motion the only state cue.

## Before you finish

Check the [never-ship table](better-animation/principles.md#never-ship).

## Reporting

**Severity.** Use the motion severity table in
[review-and-planning.md](better-animation/review-and-planning.md#vet-every-finding).

**Verification.** Exercise entry, exit, rapid repetition, reversal, and content
changes. Check reduced motion, keyboard, and touch or gestures where relevant. Watch
at normal speed, then at 2–5× duration or in the animation inspector, and step
coordinated properties frame by frame. Test gestures on a real device and profile
under representative load before claiming smoothness. Run the project's checks.
Source inspection alone cannot establish how motion feels; report what still needs a
feel check as `Not verified`.

For a build, report briefly: the frequency and purpose decision, the mechanism,
properties, curve, and duration or spring chosen. For reviews, use [the shared review
format](better-ui-review.md#report).
