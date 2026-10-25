# Interface craft

Use this to create or redesign a surface, or to raise its overall quality. It sets
the direction, composes the screen, and routes each decision to the reference that
owns it. A focused repair can go straight to its domain reference.

Domain detail lives elsewhere: structure in [layout](better-layout.md), text in
[typography](better-typography.md), palette in [colors](better-colors.md), surfaces
and icons in [polish](better-polish.md), flows and feedback in
[interaction](better-interaction.md), copy in [writing](better-writing.md), controls
in [accessibility](better-accessibility.md), motion in [animation](better-animation.md),
audio in [sound](better-sound.md), and mobile browser behavior in [PWA](better-pwa.md).
Landing pages start with [landing page](better-landing-page.md); audits use
[review](better-ui-review.md). Load each only as its decisions arise.

## Treat taste as trained judgment

Taste is a trained instinct, not personal preference: the ability to see what
elevates a surface and say why. Study interfaces that feel right, reverse-engineer
the decision behind each detail, and apply the reason rather than the look. Justify
every choice from the product, its users and the direction, never from "it looks cool".

## Do the work nobody notices

Most good details are invisible: when something behaves exactly as people assume,
they move on without a thought. Concentric radii, shared edges, stable numbers,
preserved context and handled edge cases each go unnoticed; together they make a
surface feel right. Do that unseen work anyway; it compounds.

## Set the direction before composing

Inspect the existing screen, nearby components, real content and supplied references.
Identify who uses it, their primary task and the desired character, then turn
adjectives into visible choices: "calm" might mean restrained contrast, generous
grouping and quiet feedback; a dense operations tool needs compact rows, aligned
values and a clear selection state. A direction is ready when it names the focal
content, primary action, density and distinguishing treatment.

## Extend the product's language

For an established product, extend its visual language, tokens, components and
styling system; never add a second system to land one improvement. For a new
surface, choose a coherent direction from the brief and assets. When the user wants
their own style, or neither project nor brief sets a direction, load
[basteau-taste](basteau-taste.md) as the direction. State consequential assumptions
and proceed; ask only when audience, purpose or brand is ambiguous enough to change
the design.

## Explore real variants while the direction is open

When several directions are defensible and the choice matters, build three genuinely
different ones instead of arguing in prose. Vary them along one named axis, render
each full size in its real context with real content, compare, and let the user
pick; then promote one and remove the rest. The practice is in
[variants.md](better-craft/variants.md).

## Compose with real content

Establish hierarchy through proportion, alignment, grouping and type contrast before
any surface effect. Give each section a reason to exist and order reading toward the
task. Use representative names, values, descriptions, item counts and images; lorem
ipsum and three tidy rows make every structure look good.

## Let a few details carry identity

Choose imagery for subject, crop and focal point, and keep those choices as the
viewport changes. Express character through a few intentional details whose
prominence fits the product, not interchangeable decoration, and keep familiar
controls recognizable. Repeat the chosen spacing, radius, icon and elevation
relationships so the surface reads as one system.

## Keep every part in one personality

Motion, type, color, copy and even names should share one character: a playful
product can be softer and bouncier, a professional tool crisp and fast. When a detail
fights the rest, change it to fit rather than adding another to balance it.

## Let frequency set the budget for expression

The more often something is seen or used, the quieter and faster it should be. Daily
tools and repeated actions get restraint; rare moments such as onboarding, first run
or completion can carry delight. Motion specifics are in [animation](better-animation.md).

## Design every state, not the default screen

Carry the visual language through empty, loading, error, selected, disabled and
success states, and preserve useful context across changes. Handle edge cases
invisibly: long and missing values, timers that pause in a hidden tab, hover that
survives the gaps between items. A beautiful default screen is one state of the
experience.

## Ship excellent defaults over options

Most people never customize, so the default must be the good version. Prefer one
well-chosen behavior to a setting that defers the decision, and keep component
setup minimal.

## Render, compare, refine

Inspect the result in a browser or platform preview with real content at
representative wide and narrow sizes; for an existing screen, compare before and
after in the same state and viewport. Against a supplied reference, compare
hierarchy, density, proportion and character, not only values. Fix the largest
mismatch with the direction at its cause, from composition to type and color, then
surface and motion. Return with fresh eyes when you can, and walk every state slowly.

## Finish against the direction

Finish when the surface expresses the direction, the primary flow and affected states
work with supported inputs, and visible defects in the inspected scope are fixed or
reported. Run the project's relevant checks. Source inspection cannot establish
visual quality: without a preview, complete the code-verifiable work and state that
rendered appearance is unverified. Report briefly; a creation task needs no review
verdict.
