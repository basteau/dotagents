# Motion principles

The craft bar: motion that feels right, not motion that merely runs. A transition
that works but feels sluggish, grows from the wrong point, fires too often, or drops
frames is a regression. Restraint is the defining trait; the best result is
sometimes no animation, and deciding that is a success rather than a dodge. Most of
these details go unnoticed on purpose: when an interface behaves exactly as people
assume, they move on, and the aggregate of that invisible correctness is what makes
it feel loved.

## Decide in this order

Each step gates the next. Do not choose a curve before knowing whether the element
animates at all, and never offer motion options as a menu: make the call and state
the reason in a line.

| Step | Question | Stop or change course when |
| --- | --- | --- |
| 1. Should it animate? | How often will a person see this? | It is high-frequency or keyboard-driven |
| 2. Purpose | Which job does the motion do? | No purpose can be named |
| 3. Mechanism | What is the cheapest tool that works? | A static affordance or existing primitive already does it |
| 4. Timing | Easing and duration, or a spring? | The moment only works slow and showy |
| 5. Properties | Which properties, from which origin? | It needs layout properties without a real layout change |
| 6. Interruption and exit | What happens when it reverses or leaves? | It restarts from zero or leaves invisible controls live |
| 7. Reduced motion and input | What do reduced motion, touch, and keyboard get? | The final state or feedback depends on the motion |

## Gate on frequency

| Frequency | Decision |
| --- | --- |
| 100+ times a day: shortcuts, command palette toggle, keyboard navigation, typing | No animation |
| Context menus and other summoned, ephemeral lists | Appear instantly; an exit fade is optional |
| Tens of times a day: hover, list navigation, frequent toggles | Near-imperceptible and brief, or static feedback |
| Occasional: dialogs, drawers, menus, toasts, settings | Standard animation |
| Rare or first-time: onboarding, empty states, success, celebration | The delight budget lives here |

Shortcut-triggered and keyboard-navigation changes are instant: a palette used
hundreds of times a day should not open with a transition. Activating an ordinary
control with Enter or Space is not a shortcut; it gets the same feedback as a click.
Daily use argues for less motion, not more. When usage is unknown, state the assumption.

## Name the purpose

| Purpose | Example |
| --- | --- |
| Feedback | A button scales slightly on press; a hold fills while pressed |
| Spatial consistency | A popover grows from its trigger; a toast leaves by the edge it entered |
| State indication | A button morphs into its pending or done state |
| Preventing a jarring change | Content that would teleport, appear, or vanish gets a bridge |
| Explanation | A demonstration of how a feature works, in marketing or onboarding |
| Delight | Only at the rare tier |

"It looks cool" is not a purpose on anything seen often. Check function too: data a
person is reading or acting on does not move for style. A pointer-tracking flourish
belongs on a marketing page, not on a chart in a banking app.

## Make it physically plausible

- Nothing appears from nothing. Never enter from `scale(0)`; start from 0.9–0.97
  with transparency, or use a plain fade when movement adds nothing.
- Grow anchored surfaces (popover, menu, tooltip, select) from their trigger, and
  follow the placement when it flips. Unanchored dialogs stay centered.
- Keep deformation subtle: press feedback around 0.97 (0.95–0.98), any squash or
  stretch within 0.95–1.05. Scaling a container scales its text and icons too.
- Exit the way it entered. Symmetric paths make swipe-to-dismiss feel obvious and
  keep the spatial story intact.
- Real things slow before they stop: rising resistance past a boundary instead of a
  hard wall, and released motion that keeps its velocity. See [gestures.md](gestures.md).

## Stage one thing at a time

Give the eye one focal point. Two elements animating prominently at once compete;
let one lead and keep the rest still or quiet. A modal dims what is behind it so
the focus is unambiguous, with backdrop and surface timed together. Animated
surfaces stay in their correct stacking layer for the whole animation, exit
included, so a tooltip never slides behind the content it describes.

Sequence a group only when order explains hierarchy, with short gaps and a capped
total. Stagger is decoration: interaction never waits for it.

## Keep timing consistent and asymmetric

Similar elements share identical timing and curves, ideally from shared tokens; five
almost-matching curves are one consolidation finding. Time phases by who is acting:
slow where the person decides (a hold-to-confirm fill), fast where the system
responds (the release snaps back). Exits are usually shorter and subtler than entries.

Speed is perceived as much as measured. Ease-out starts moving at once, so it feels
faster at the same duration; a quicker spinner makes the same wait feel shorter.

## Match the product's personality

Motion is part of the brand. A playful consumer product can afford a little bounce;
a professional dashboard stays crisp and fast. A deliberately elegant component may
run slightly slower on a softer curve, as long as the whole experience is in harmony:
easing, duration, visual design, and even the name. One bouncy component in a crisp
app is a cohesion defect. When unsure whether motion feels right, the strongest
move is often to delete it.

## The twelve principles, for interfaces

The classic animation principles still apply; interfaces use them at a much smaller
amplitude.

| Principle | In an interface |
| --- | --- |
| Squash and stretch | Subtle scale on press or landing, within 0.95–1.05 |
| Anticipation | Hover and press states that signal what will happen; a visible wind-up only in expressive moments |
| Staging | One focal point; dimmed backdrops; nothing competing with the primary change |
| Straight ahead and pose to pose | Define start and end states and let interpolation fill them; prefer state-driven transitions over hand-placed frames |
| Follow-through and overlapping action | Springs that settle; children lagging a few tens of milliseconds behind their parent |
| Slow in and slow out | Easing: ease-out for arrivals, ease-in-out for on-screen travel, linear only for time |
| Arcs | Long travel, such as a shared element, can follow a gentle curve; short UI moves stay direct |
| Secondary action | An icon morph or count change that supports the main change without competing |
| Timing | Duration conveys weight and responsiveness; user-initiated UI stays under 300ms |
| Exaggeration | Reserved for rare celebration; routine UI stays realistic |
| Solid drawing | Consistent depth, light, shadow, and perspective across moving layers |
| Appeal | Cohesion: motion that fits the product's personality |

## Never ship

| Never | Instead |
| --- | --- |
| Animation on a shortcut, keyboard navigation, or 100+/day action | Instant change |
| `transition: all` | Name the exact properties |
| Entrance from `scale(0)` | 0.9–0.97 scale plus opacity, or a plain fade |
| Ease-in on an entrance or response | Ease-out or a strong custom curve |
| Weak built-in easing on a deliberate animation | A strong curve from [timing-and-performance.md](timing-and-performance.md) |
| Routine UI motion over 300ms without a reason | 150–250ms |
| Center origin on a trigger-anchored surface | Origin at the trigger (dialogs exempt) |
| Keyframes on toasts, toggles, or rapidly repeated changes | Retargeting transitions or springs |
| Layout properties for movement or resizing | Transform and opacity; layout only for real reflow |
| Hover motion without hover-capable input gating | Gate it; keep press feedback for touch |
| Movement without a reduced-motion variant | Gentler variant: drop travel and scale, keep comprehension |
| A group entering all at once where order matters | 30–50ms stagger with a capped total |
| Symmetric timing on a hold or press-and-release | Slow deliberate phase, snappy response |

## Check the feel

Feel cannot be settled from code alone. Play the motion at 2–5× its duration or in
the animation inspector, and step coordinated properties frame by frame to catch
drift: do colors double-expose, does the curve stop abruptly, is the origin right,
do opacity and transform stay in sync? Test gestures on a real device. Look again
the next day; imperfections invisible during development surface with fresh eyes.
Some balances, such as opacity against height in an entering list, have no formula:
adjust until it feels right and say that a feel check is still needed.
