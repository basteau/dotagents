# basteau taste

A default visual direction for sites that have no stronger brief: a quiet ground,
one confident gesture, and a few crafted details that reward attention instead of
demanding it. Pages feel calm at first glance and personal on the second. Character
comes from proportion, a signature detail rooted in the brand, and restraint, not
from stacking effects. Solutions stay simple: CSS first, static where possible,
simplicity over perfection.

Apply it as a set of decision defaults. The project's brand, tokens, fonts and
stack always win; keep this structure and swap the values. Mechanics live
elsewhere: type scales and rendering in [typography](better-typography.md), ramps
and contrast in [colors](better-colors.md), spacing in [layout](better-layout.md),
easing and springs in [animation](better-animation.md), sound design in
[sound](better-sound.md), surfaces and icons in [polish](better-polish.md), the
working loop in [craft](better-craft.md) and copy in [writing](better-writing.md).
Evidence and starter tokens are in [evidence.md](basteau-taste/evidence.md) and
[starting-values.md](basteau-taste/starting-values.md).

## Lead with one gesture per view

Pick the single thing a visitor should remember: an oversized wordmark, a full-bleed
photograph of the craft, or one sentence. Make it big and everything else small.
A view with two heroes has none. Ask "what is the one gesture?" before composing.

## Keep the ground quiet

Use a small neutral set with a job per step: ground, ink, muted, rule. Near-black
ink (`#0B0B0C`) instead of pure black; ground is white, or a warm off-white such as
bone (`#F3F0E8`) when the brand is warmer. Rules and hairlines are ink at 12-16%
alpha. Give light and dark themes explicit tokens and a matching `theme-color`, or
commit to one theme deliberately.

## One accent, used once per view

A brand may own several hues; let them live in the signature element (the mark, a
tile field, the photography). In the interface, one accent marks the primary action;
secondary actions stay neutral. If two things wear the accent in one view, demote one.

## Contrast type by extremes, not by variety

One sans family carries the page. Hierarchy comes from size and weight extremes:
display at 800 with tight tracking and a tight line box, body and taglines at 300,
labels at 500-600. A small tracked uppercase eyebrow (11-13px, mono or sans, muted)
sits above the display. A second face is allowed for one expressive role only: a
condensed display serif for an editorial brand, a script for a single word.

## Few words, a human voice

A first screen needs a role line, a one-line promise and one action. Write the
promise like a person ("Turning ideas into code."), verbs on buttons ("Say hello",
"Contact us"), and never offer two paths to the same thing. Prose sites speak in
the first person with plain, slightly wry sentences. One human line in the footer
is enough personality there.

## Tight clusters in generous space

Group the copy into one compact block (16-40px internal gaps) and surround it with
real emptiness: about 96px page padding on desktop, 24px on phones. Prose sits in
one column near 520-640px. Lists read as rows: title left, muted meta right in
tabular figures, separated by space or one hairline, not boxes.

## One signature detail, derived from the brand

Each site earns one memorable detail, two at most, found in its name, mark or craft:
letters that hold water because the name ends in "eau", a mirrored letter pair that
flips once, a field of tiles built from the logo. It must not cover content, must
work when ignored, and must survive reduced motion as a still image. Never import a
signature from another site; find this brand's own.

## Reward attention, never demand it

Details reveal themselves to the curious: a field that swells near the cursor, a
click that ripples, a hover fringe on a link, a small counter. Idle states stay
calm: no random twinkle, no looping attention-seekers outside the signature
element. Interactive play is welcome when it is about the subject (a memory game of
the tools you use), never as a gate in front of the content.

## Spend a small motion budget

| Moment | Starting value |
| --- | --- |
| Entrance | content chunks stagger 70-100ms; 420-520ms; opacity + 8-12px rise + 4px blur |
| Hover, press | 150-200ms ease-out; press scale .96-.97; arrows nudge 2px |
| Exit | shorter and softer than the entrance (~150ms) |
| Occasional action | one real moment, e.g. a theme reveal growing from its button (~850ms) |
| Ambient | slow loops (4-8s) inside the signature element only |

Default curve `cubic-bezier(.23, 1, .32, 1)`; a slight overshoot only for playful
turns. Reduced motion keeps fades and stills; ambient motion stops.

## Sound is rare and deliberate

Silence by default. When a site has sound, only deliberate clicks on the main action
or the signature make it; no hover sounds or per-item ticks, nothing for
reduced-motion visitors. A game may have more, behind a visible mute control.

## Texture at the scale of a pixel

Prefer material over decoration: a soft-light print grain on colored surfaces, a
1px chroma fringe on a wordmark, a faint dot grid filling an empty middle, real
photography with grain. Avoid glows, glass and gradient blobs as identity.

## Pills, hairlines and quiet controls

Primary actions are pills (fully rounded, 50-54px tall, 600 weight). Links carry a
1px hairline underline in the rule color that wipes to full ink on hover. Icon
buttons are bare until hovered. Focus is a 2px ink outline with an offset. Paired
states (arrow and check, mute and sound) share one slot and crossfade with scale and
blur. Hit areas reach 44px through pseudo-elements, not bigger visuals.

## CSS first, simplicity over perfection

Reach for CSS transitions, `@property`, `text-shadow`, masks and `clip-path` before
JavaScript; add script only for state, measurement or play. A brochure site can be
static files with no build. Accept a sub-pixel imperfection rather than a fragile
system to remove it, and delete an effect that needs constant tuning.

## Finish the edges

Ship the parts nobody asks for: a 404 in the same language, favicon and touch icon
from the mark, an OG image, both themes, a composed (not merely stacked) phone
layout, and click targets checked where a tight display line box overhangs links.

## What this taste avoids

- Template heroes followed by three icon feature cards.
- Gradient blobs, glassmorphism, neon glows and cursor spotlights as decoration.
- Several accent colors in the interface chrome; mid-gray body text on gray.
- Effects added for effect's sake, random twinkle, scroll-jacking that fights the wheel.
- Hover sounds, autoplaying audio, success fanfares for ordinary actions.
- Duplicate calls to action, carousels, stock illustration, emoji as icons.
- Generic copy ("Elevate your digital presence") and inflated claims.
- Heavy frameworks and builds for a page that is a few static files.

## Adapt by site type, keep the brand

| Site type | The one gesture | Density and signature | Motion and sound |
| --- | --- | --- | --- |
| Personal portfolio | Oversized name or a first-person sentence | Sparse; playful signature, a game is fine | Full budget; sound optional with mute |
| Studio or freelancer | Wordmark plus one contact action | Sparse; signature from the mark | Entrance, hover; sound on the CTA at most |
| Café, bakery, maker brochure | Photography of the product or craft | Warm ground from the brand; hours, menu and location plain and scannable | Gentle reveals; no sound |
| Product page | The product itself: screenshot, demo, object | Medium; accent is the brand primary | Motion demonstrates the product |
| Case study or editorial | The prose column and its images | One column, inline tags or pills | Minimal; motion only in embedded demos |
| Docs | Readable type and navigation | Dense, hairline rhythm; signature only in the header | Near zero |

When a brand exists, its palette, faces and voice replace the starting values. What
transfers is the structure: quiet ground, one gesture, one accent, a signature found
in the brand, a small motion budget, finished edges. Before shipping, check that the
page still works with every effect off and that nothing can be removed without loss.
