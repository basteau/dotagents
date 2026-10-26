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

## Give every choice a reason from the product

Each visible decision should have a one-line reason drawn from the product, its
users or the direction; "it looks cool" and "it was the default" are not reasons.
When borrowing from a reference, name the decision behind the detail and apply
that, never the look alone.

## Set the direction before composing

Inspect the existing screen, nearby components, real content and supplied references.
Identify who uses it, their primary task and the desired character, then turn
adjectives into visible choices: "calm" might mean restrained contrast, generous
grouping and quiet feedback; a dense operations tool needs compact rows, aligned
values and a clear selection state. A direction is ready when it names the focal
content, primary action, density and distinguishing treatment.

## Extend the product's language

For an established product, extend its visual language, tokens, components and
styling system; never add a second system to land one improvement. The project's
DESIGN.md, when present, is the direction; a new site without one decides its style
through [better-design-md](better-design-md.md) first. When a brief is vague, decide,
state your reading in one line and proceed; ask only when a missing fact blocks the
work or would change the design.

## Know whether it is read or worked in

The more often something is seen, the quieter and faster it should be. Decide first
which kind of surface this is; a product page that shows its app is a page whose
interface fragments follow app-screen rules.

| | Page: read and scrolled | App screen: worked in daily |
| --- | --- | --- |
| Leads with | The idea: the subject, a headline, a picture | The block that answers the screen's one question |
| Title | Display size, says what this is | A noun title near body scale, no hero header |
| Color | Carried by the work or whole fields; an accent on the action | On the data and at most one control, never the chrome |
| Expression | One or two signature moves; staged motion | Restraint; delight only at rare moments such as first run or completion |
| Starts from | [landing page](better-landing-page.md) | This file and [interaction](better-interaction.md) |

On an app screen, write the screen's one question and today's one finding ("the
23:28 deploy doubled the error rate"): the largest block answers the question, the
finding sits under the title beside the action it drives, and the most frequent job
is one click from the first screen.

## Explore real variants while the direction is open

When several directions are defensible and the choice matters, build three genuinely
different ones instead of arguing in prose. Vary them along one named axis, render
each full size in its real context with real content, compare, and let the user
pick; then promote one and remove the rest. The practice is in
[variants.md](better-craft/variants.md).

## Compose with real content

Establish hierarchy through proportion, alignment, grouping and type contrast before
any surface effect, and order reading toward the task. Use representative names,
values, item counts and images; lorem ipsum and three tidy rows make every structure
look good. Never fake the subject: no stock stand-in for the product and no
dashboard of gray bars, but a real cropped fragment with data that adds up, or the
subject drawn. Choose each image for subject, crop and focal point, and keep those
as the viewport changes.

## Take identity from the subject, not the category

List what only this product or client has: its name and what it means, its place,
its numbers and constraints, its objects, materials, rituals and the words of its
trade. Draw the few details that carry identity from that list, and test each by
swapping in the nearest competitor: if it still fits, it belongs to the category.
Let the work carry the color; a near-neutral interface lets real media and data be
the vivid part. Keep familiar controls recognizable, and repeat the chosen spacing,
radius, icon and elevation relationships so the surface reads as one system.

## Restrain ornament, be generous with substance

Removing template devices (an eyebrow over every heading, icon cards, gradients,
hairlines) is half the job; a surface that loses them and gains nothing reads thin
and unfinished. Fill the space with scale, the full content, real pictures of the
subject's world or a whole field of color, never another ornament. An empty area is
a rest only when something closes it; otherwise it is a hole.

## Keep every part in one personality

Motion, type, color, copy and even names should share one character: a playful
product can be softer and bouncier, a professional tool crisp and fast. When a detail
fights the rest, change it to fit rather than adding another to balance it.

## Design every state, not the default screen

Carry the visual language through empty, loading, error, selected, disabled and
success states, and preserve useful context across changes. Handle edge cases
invisibly: long and missing values, timers that pause in a hidden tab, hover that
survives the gaps between items. A beautiful default screen is one state of the
experience.

## Do the work nobody notices

Fix the details people only see when they are wrong: concentric radii, shared
edges, digits that keep their width, context preserved across changes, long and
missing values handled. Check each in the render; none is worth a sentence in the
report, and together they decide whether the surface feels right.

## Ship excellent defaults over options

Most people never customize, so the default must be the good version. Prefer one
well-chosen behavior to a setting that defers the decision; add an option only
when users with different needs would each be wrong with the other's default.

## Render, compare, refine

Screenshot the result with real content at the widths
[critique](better-ui-review/critique.md#judge-the-pictures-not-the-code) names and
judge the pictures, not the code. For an existing screen, compare before and after in
the same state and viewport; against a supplied reference, compare hierarchy,
density, proportion and character, not only values. Fix the top three problems at their cause, in the order
idea, hierarchy, composition, type, imagery, color, detail, motion, then look again;
three rounds at most. The passes are in [critique](better-ui-review/critique.md).

## Finish and report

Finish when the surface expresses the direction, the primary flow and affected states
work with supported inputs, and visible defects in the inspected scope are fixed or
reported. Run the project's relevant checks. Source inspection cannot establish
visual quality: without a preview, complete the code-verifiable work and state that
rendered appearance is unverified. Report briefly; a creation task needs no review
verdict.
