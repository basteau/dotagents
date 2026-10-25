# Motion vocabulary

Reverse lookup: turn a loose description of a motion effect into the term to ask a
designer, a library, or an AI for. Read for the sensation people describe
("springy", "slides off", "draws itself in"), not their keywords.

Answer with the term first and a one-line meaning. If two terms compete, give the
best match and one or two alternates with the distinction. If nothing fits, name
the closest term, say it is an approximation, or compose terms ("a stagger of
scale-in entrances"). Naming an effect does not recommend it; choose implementation
and intensity only when asked to design or build.

## Entrances and exits

| People say | Term | Meaning |
| --- | --- | --- |
| "It just fades up" | Fade in / fade out | Appears or disappears through opacity alone |
| "It comes in from the side" | Slide in | Enters by travelling in from an edge |
| "It grows into place" | Scale in | Grows from slightly smaller to full size, usually with a fade |
| "It bounces in a little" | Pop in | Scale in with a slight overshoot before settling |
| "It's uncovered bit by bit" | Reveal | Content is exposed progressively, usually by a clip or mask |
| "What it does when added or removed" | Enter / exit animation | The motion played on insertion or removal |

## Sequencing and timing

| People say | Term | Meaning |
| --- | --- | --- |
| "One after another, like dominoes" | Stagger | Each item starts a short, fixed delay after the previous one |
| "Several things moving as one" | Orchestration | Timing multiple animations so they read as a single motion |
| "The in-between frames" | Interpolation / tween | Generating every intermediate value between two states |
| "Points along the way" | Keyframes | Defined intermediate states the engine fills between |
| "Waits, then starts" | Delay | Time before an animation begins |
| "Keeps the end state" | Fill mode | Whether the first or last frame's styles persist outside the animation |
| "Ticks in steps, like a clock" | Stepped animation | Progress in discrete jumps instead of continuously |

## Movement and transforms

| People say | Term | Meaning |
| --- | --- | --- |
| "Moves over" | Translate | Shift along the x or y axis |
| "Gets bigger or smaller" | Scale | Resize visually without changing layout |
| "Spins" | Rotate | Turn around a point |
| "Slants" | Skew | Shear the element out of its rectangle |
| "Tilts towards me", "flips like a card" | 3D tilt / flip | Rotation around x or y with depth |
| "How strong the 3D looks" | Perspective | Viewer distance; lower values exaggerate depth |
| "The point it grows from" | Transform origin | Anchor for scale and rotation |
| "Grows out of the button I clicked" | Origin-aware animation | A surface scales from its trigger instead of its own center |

## Transitions between states

| People say | Term | Meaning |
| --- | --- | --- |
| "One fades into the other in place" | Crossfade | Outgoing fades out while incoming fades in at the same spot |
| "The same box just gets bigger" | Continuity transition | Before and after are visibly connected so orientation holds |
| "One shape becomes another" | Morph | Continuous change of shape, like a dynamic island |
| "The thumbnail flies into the detail view" | Shared element transition | One element travels and transforms between two views or containers |
| "Things slide over to make room" | Layout animation | Elements animate to new positions or sizes instead of snapping |
| "Section opens and closes" | Accordion / collapse | Height expands and collapses to show content |
| "Forward goes left, back goes right" | Direction-aware transition | Travel direction encodes navigation direction |
| "The text changes letter by letter" | Text morph | Characters animate individually when a value changes |

## Scroll and navigation

| People say | Term | Meaning |
| --- | --- | --- |
| "Things appear as I scroll down" | Scroll reveal | Entrance triggered once content enters the viewport |
| "Moves exactly with my scroll" | Scroll-driven animation | Progress is bound to scroll position, not time |
| "Background moves slower than the front" | Parallax | Layers travel at different rates to suggest depth |
| "Animation between pages" | Page transition | Motion played on route change |
| "The browser morphs one page into the next" | View transition | Platform-captured before and after states animated together |

## Feedback and interaction

| People say | Term | Meaning |
| --- | --- | --- |
| "Changes when the mouse is over it" | Hover effect | Response to a hovering pointer |
| "Shrinks a bit when clicked" | Press / tap feedback | Subtle scale-down that makes a control feel physical |
| "Fills up while I hold it" | Hold to confirm | Progress that completes only if the press is held |
| "Pick it up and move it" | Drag | Direct manipulation, often with momentum on release |
| "Rearrange the list by dragging" | Drag to reorder | Siblings shift to make room for the dragged item |
| "Flick it away to close" | Swipe to dismiss | Dragging a surface off screen dismisses it |
| "Stretches and snaps back past the end" | Rubber-banding | Rising resistance beyond a boundary, then return |
| "Shakes no" | Shake / wiggle | Quick lateral jitter for rejected input |
| "Circle spreading from my tap" | Ripple | Expanding circle from the contact point |
| "Keeps going after I let go" | Momentum | Released motion carries its velocity forward |

## Easing and springs

| People say | Term | Meaning |
| --- | --- | --- |
| "How it speeds up and slows down" | Easing | Rate of change of progress over time |
| "Fast then gentle" | Ease-out | Starts fast, ends slow; the default for UI responses |
| "Slow then fast" | Ease-in | Starts slow; usually feels sluggish on UI |
| "Slow, fast, slow" | Ease-in-out | Suits elements already on screen moving A to B |
| "Robotic, constant speed" | Linear | Uniform progress; for time, spinners, marquees |
| "Custom curve" | Cubic Bézier | Easing defined by two control points |
| "Accelerates and brakes differently" | Asymmetric easing | Unequal acceleration and deceleration; feels more alive |
| "Springy, physical" | Spring | Motion from stiffness, damping, and mass instead of a fixed duration |
| "Snappier pull" | Stiffness / tension | Strength of the pull toward the target |
| "How fast it stops wobbling" | Damping | Lower damping bounces and oscillates more |
| "Feels heavy" | Mass | More mass is slower and more sluggish |
| "Overshoots and settles" | Bounce | Visible overshoot before rest |
| "When it looks finished" | Perceptual duration | How long a spring appears to take, ignoring micro-settling |
| "Can change its mind mid-way" | Interruptible animation | Redirects from its current state and velocity without finishing first |

## Looping and ambient motion

| People say | Term | Meaning |
| --- | --- | --- |
| "Scrolling ticker of logos" | Marquee | Content scrolling continuously in a loop |
| "Repeats" | Loop | Plays a set number of times or forever |
| "Back and forth" | Alternate / yoyo | Each loop reverses instead of jumping to the start |
| "Circles around something" | Orbit | Continuous circular path around another element |
| "Gently throbs" | Pulse | Repeating scale or opacity change to draw attention |
| "Bobs up and down" | Float | Slow continuous drift that suggests weightlessness |
| "Moves while I'm not touching it" | Idle animation | Ambient motion while waiting for interaction |

## Polish and effects

| People say | Term | Meaning |
| --- | --- | --- |
| "Softens the swap" | Blur | A filter that softens edges or hides a double exposure |
| "Cut to a shape" | Clip-path | Hard-edged clipping, used for reveals and wipes |
| "Fades out at the edges" | Mask | Clipping by an image or gradient, with soft edges |
| "Slider comparing two images" | Before/after slider | Draggable divider wiping between two overlaid layers |
| "Draws itself" | Line drawing | An SVG stroke traced progressively |
| "Grey placeholder with a sheen" | Skeleton / shimmer | Loading placeholder with a moving highlight |
| "Numbers roll up" | Number ticker | Digits counting or rolling to a value |
| "Digits don't jiggle" | Tabular numbers | Fixed-width figures so changing numbers stay aligned |
| "Types itself out" | Typewriter | Characters appear one at a time |

## Performance and principles

| People say | Term | Meaning |
| --- | --- | --- |
| "It stutters" | Jank / dropped frames | Frames missed their deadline, causing hitches |
| "How smooth it is" | Frame rate | Frames per second; 60 baseline, 120 on newer displays |
| "Runs on the GPU" | Compositing / hardware acceleration | Moving or fading a layer without redoing layout or paint |
| "Prepare it to animate" | `will-change` | Hint to promote an element to its own layer ahead of time |
| "Animating width kills it" | Layout thrashing | Per-frame layout recalculation from layout properties |
| "A little wind-up first" | Anticipation | Small opposite motion before the main move |
| "Keeps wobbling after it stops" | Follow-through | Parts settle slightly after the main motion ends |
| "Squishes when it lands" | Squash and stretch | Deformation that conveys weight and speed |
| "Feels faster than it is" | Perceived performance | Motion that shortens how a wait feels |
| "Never lose track of it" | Spatial consistency | An element keeps identity and position across states |
| "Respect the OS setting" | Reduced motion | Toning down movement for people who request it |

## Close terms

| If unsure between | Distinguish by |
| --- | --- |
| Pop in vs bounce | Pop in is an entrance; bounce is overshoot in any spring motion |
| Clip-path vs mask | Hard geometric edge vs soft, gradient or image edge |
| Shared element vs layout animation | Travels between views or containers vs moves within one layout |
| Morph vs crossfade | Shape transforms continuously vs two states overlap in place |
| Scroll reveal vs scroll-driven | Triggered once on entry vs progress bound to scroll |
| Momentum vs rubber-banding | Carrying release speed vs resisting past a boundary |
| Ease-out vs spring | Fixed duration and curve vs physical parameters and velocity |
| Page vs view transition | Any route-change motion vs the platform API that captures both states |
