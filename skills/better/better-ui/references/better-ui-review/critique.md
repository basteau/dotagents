# Design critique

Judge whether a rendered surface is good, not only whether it is correct: for a
requested critique or design verdict, a "does this look generated?" check, and each
round of [craft's render loop](../better-craft.md#render-compare-refine). Defects
with evidence still go in [the shared report](../better-ui-review.md#report);
scores and tells are judgment, labelled as such and kept apart from findings.

## Judge the pictures, not the code

Screenshot the first screen and the whole page at about 1440, 768 and 390px, and
judge only what they show. Whoever wrote or read the code reads its intentions into
it; a problem visible in a screenshot is real whatever the code says. Without a
renderer, say so and give no scores.

## Look in separate passes

Make each pass on its own; blended, they miss things.

| Pass | Ask | Fails when |
| --- | --- | --- |
| One second | Glance and look away: what did you see? | The answer is "a website": there is no focal point |
| Five seconds | As a stranger: what is this, for whom, what next? | The title only makes sense after scrolling |
| Squint | Do two or three masses lead, with clear size steps? | Many similar medium masses |
| Cover the logo | Could this belong to the nearest competitor? Does the idea reach pricing, FAQ and footer? | Swapping the name gives an equally valid page, or stock blocks follow the hero |
| Edges | How many distinct left edges run down the page? | More than three, placed one at a time |
| Space and frame | Are section gaps larger than gaps inside sections, the side margins equal, every void closed? | A hole beside a short column, text in one half and nothing in the other |
| Type | Is the jump from body to headline decisive, about three times or more? | Five or more near sizes, orphan words, wide or centred paragraphs |
| Color | Which color holds the surface, which supports, where is the accent? | The accent appears in ten places and means nothing |
| Images | Is each the real subject, doing a job, in shared ratios and one grade? | Gray boxes, stock stand-ins, fake screens of bars |
| Sameness | Write one word for each section's layout | The same word twice in a row, or three times on the page |
| Phone | Is the phone screen its own composition, crops and line breaks? | The desktop page squeezed |
| Substance | Does every section the brief asks for deliver its information in full, at a readable size? | A clean page that reads thin, or small gray running text |
| Collisions | Does anything lie on a headline or button at any scroll position, or scroll sideways? | Any overlap; a row cut at the edge that is not plainly deliberate |
| The live thing | What can the visitor do, or what differs each visit? | Nothing, on a page whose content offered something |

## Score like a juror

When a score is wanted, rate four criteria from 1 to 10 and weight them design 40%,
usability 30%, creativity 20%, content 10%.

| Score | Design | Usability | Creativity | Content |
| --- | --- | --- | --- | --- |
| 9–10 | Every screen holds as a poster | Clear without scrolling, flawless on a phone | An idea specific to this subject, carried through | Every line could only be about this subject |
| 7–8 | Confident and coherent, a soft spot or two | Works, with minor friction or one real problem | A clear idea with one memorable move, or a known approach done with taste | Concrete, some generic lines |
| 6 or less | Tidy but anonymous, a kit | You have to hunt, or something breaks | The category default | Slogans, filler, stock |

Featured gallery sites land around 7.5 to 8 overall, and an honest 8.5 is rare; a
first score of 9 for your own work means score again as the juror who did not build
it. Caps override the arithmetic:

- No title on the first screen saying what this is: usability at most 6.
- Anything covering a headline, a blank band where a reveal failed, or content
  clipped off-screen: usability at most 5.
- The page passes for a competitor's: creativity at most 6.
- A placeholder, lorem ipsum or a section the brief asked for reduced to a few
  words: content at most 5 or 6.
- Two or more desktop screens of mostly empty ground, or small gray running text:
  design at most 6.5.

## Name the tells of a generated surface

A tell is a device produced by default rather than decided. Most began as good ideas,
so a tell is a warning that needs a reason from the subject or the project's
DESIGN.md, not a ban, and never a defect on its own. Replace it with a procedure,
never a named font, color or component; every published "use this instead" list
becomes the next wave of tells.

| Tell | Ask for, or replace with |
| --- | --- |
| Centered headline, pill badge, two buttons, a screenshot below | A composition taken from the idea |
| A small tracked uppercase label (eyebrow) above every heading | The words in the heading or the first sentence |
| Three identical cards, each with an icon in a tinted square | A composed section with the real content |
| Gradients on grounds, buttons or text; glows; glass panels; a soft shadow under every resting card | Flat color, a tone step, a real picture |
| Violet and indigo, or cream with terracotta as the shortcut to warmth | Colors taken from the subject's own materials and place |
| Monospace micro-labels, 01/02/03 numbering, hairline grids and crop marks as ornament | Structure from space, scale and tone; a real list when order matters |
| One medium radius (12–16px) on everything | Square or fully round, chosen once |
| One headline word in italic serif, color or underline; a sentence-length headline over three lines | One face, a short title, the rest in the supporting line |
| Agency furniture: a live local clock, coordinates, "Scroll", an "Available for work" dot | A reason from the subject, or remove it |
| Hero, logos, three features, testimonial, pricing, CTA in stock order | The order of the argument |
| Invented logos, quotes, star rows, "10,000+ teams" | Real proof, or none |
| Genre copy: unlock, elevate, seamless; "not just X, it's Y"; tricolons; an em dash in every paragraph | What the thing does, specifically |
| The same fade-and-rise on every block, hover lift on every card, pulsing dots | One signature move, and feedback where the hand is |
| App screen: KPI cards with an icon in a tinted circle, a green "+12.5%" and a sparkline each | Label over value in one divided container; a delta in words with its window, gray at zero |
| App screen: a tinted sidebar with a colored active pill, the accent on every button and tab | Chrome on the page ground; the accent on the data and at most one control |
| App screen: "Welcome back" with a waving hand, a gradient area chart, a rainbow of series | A noun title; one hue on the main data, three to five faint gridlines |
| App screen: placeholder people, round vanity totals, zebra rows under uppercase headers | The product's data at its real size, totals summed from rows, right-aligned tabular figures |

Removing every tell and adding nothing produces an empty page with good manners,
which is a tell too. The tells were filling a gap where an idea should have been; see
[craft](../better-craft.md#restrain-ornament-be-generous-with-substance).

## Write the critique down

Use one shape so rounds can be compared:

```text
Round n
Seen in one second:
Idea, in one sentence:
Live thing:
Layout word per section: hero=… / …
Scores: design · usability · creativity · content → overall, caps applied
Top three problems, most visible first, each with its screenshot and fix
```

Name problems as a designer would to a colleague: "the supporting paragraph starts
40px left of the headline's edge", "the phone hero crops the bottle out of frame".
"Could be more polished" is not a finding.

## Fix in order, then stop

Fix the idea, then hierarchy, composition, typography, imagery, color, detail and
motion; a later fix cannot rescue an earlier failure. When removing something leaves
a gap, close it, enlarge what remains, or give it to a picture or a color field.
Stop after three rounds, or sooner when a round finds nothing a stranger would
notice; if the last round still falls short, say so with the remaining problems.
