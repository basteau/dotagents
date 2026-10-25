# Hero and first screen

The first screen sets identity, promise and the next step. It does not need to hold
every element on every device, but a visitor must be able to say what this is,
whose it is and what to do next before scrolling.

## Identity in one screen

| Ingredient | What it does | Examples |
| --- | --- | --- |
| One plain sentence | Says what and who, in the owner's voice | "I'm Ana, a product designer who prototypes tools for play." "Fresh pasta, made every morning in Leuven." |
| The real subject | Shows instead of describes | The product working, the work, the dish, the room, the people |
| One signature detail | Makes it this brand and no other | A handwritten name in a sans sentence, a display line with one letter dropped off the baseline, a crop that only this photographer would choose, a mark that recurs as a motif |
| A credential line | Answers "why trust them" quietly | "Previously at …", "Since 2009", "Featured in …", set small beneath the statement |
| A small, familiar nav | Lets the content lead | A wordmark, three to six links, one action |

Keep the signature to one or two moves. A second competing gesture cancels both.

## Composition patterns

Choose the pattern that fits the subject and the evidence you actually have.

| Pattern | Fits | Make it work |
| --- | --- | --- |
| Cinematic full bleed: film or photo fills the screen, headline set large and low over it | Food, places, physical products, mission brands | A tactile, close subject; text over a calm area of the image; a poster frame that still reads before the video loads; a pause control |
| Statement over work: one large sentence, the work grid directly below | Portfolios, studios | Start the grid within the first screen so the work is the proof; label each piece with title, type and client |
| Quiet column: name, one-line role, short bio with inline links to past teams and contact | Personal sites, writers, engineers | Generous margins, small confident type, links that carry the credentials |
| Skimmable layer over a full story: a few key phrases stand out, the full paragraph is there for readers | Bios with range, founders | The highlighted phrases alone must tell the story; the rest stays readable, not hidden behind interaction |
| Product evidence: headline and action beside a legible product view | Software, tools, apps | Crop to the moment that proves the claim; a tiny dashboard in an oversized device frame demonstrates nothing |
| Practical hero: what, when, where and the action, over the place or the event | Small businesses, events | Date, address or hours in the first screen as text, not only in an image |
| Interactive object: the hero is something to touch, play or try | Playful products, design-led studios | It works with keyboard and touch, explains itself without instructions, and the page reads without it |

## Headline and action

- Lead with the outcome, the identity or the defining fact; follow with one or two
  sentences on how, for whom or where.
- Useful shapes: "{Outcome} without {pain}", "The {category} for {audience}",
  "{What} in {place}, since {year}", "I'm {name}, {role} who {specific thing}".
  Use them to find the sentence, then rewrite it in the brand's voice.
- Mirror the promise and tone of the ad, email or post that sent the visitor.
- Break display lines where the thought breaks, never mid-phrase. Prefer balanced
  wrapping over hard breaks, and check that each width keeps phrases intact; see
  [typography](../better-typography.md).
- Cap the headline and supporting line to a readable measure; a short line can be
  set very large, a long one cannot.
- One primary action, labelled with its outcome, with any commitment beside it
  ("Free for 14 days, no card"). One quieter secondary at most.
- Put the single strongest genuine proof signal near the headline: a named client,
  a review score with its source, an attendance number, a press mention.

## Entrances

A hero may stage a short, one-time entrance: headline phrases, then supporting line,
then action and image, each a semantic chunk rather than every letter. It must
finish quickly, never block reading or clicking, never replay on return navigation,
and collapse to an instant or opacity-only state under reduced motion. An intro
sequence that covers the page needs a skip and must not hide the content from
crawlers or assistive technology. Timing and easing come from
[animation](../better-animation.md).

## Small screens

- Keep the statement and the action in the first phone screen; the image may follow.
- Re-crop images for portrait rather than shrinking a landscape composition.
- Keep video heroes light: a poster image, no autoplay sound, and a still fallback
  when data saving or reduced motion is on.
- Keep tap targets and the nav usable; a large display headline must not push the
  action below two screens.

## Hero checks

- Can someone who sees only the first screen say what it is, whose it is, and what
  to do next?
- Is the image the real subject, cropped to the moment that matters?
- Is there exactly one signature detail, and does it belong to this brand?
- Does every claim in it have evidence on the page or in the brief?
- Does it hold at phone width, at 200% zoom, and before images or fonts load?
