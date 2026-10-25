# Content, shipping and verification

What makes a landing page feel made rather than generated, and finished rather
than mocked up.

## Content realism

- Write real draft copy in the brand's voice. No lorem ipsum.
- No placeholder brands, people or places ("Acme", "John Doe", "Lorem Café"). In a
  draft, use the client's real names or mark the gap: `[client logo]`, `[quote from
  a customer]`.
- Never present invented testimonials, logos, press, ratings or statistics as real.
  A specific-looking number ("47.2% faster") is as fictional as a round one; use a
  supplied figure or leave the claim out.
- For layout testing, use representative lengths: the longest name, a two-line
  title, a missing photo, an empty list. See [layout](../better-layout.md).
- Avoid generic marketing filler: "elevate", "seamless", "unleash", "next-gen",
  "game-changer", "streamline", "in today's world". Replace each with what the
  product does.
- Sentence case for headings, active voice, no exclamation marks in confirmations,
  no "Oops" in errors; see [writing](../better-writing.md).
- Use distinct, consented photos of real people; stock smiles read as fake.

## Complete states

Every interactive element has its full state set: hover, pressed, visible focus,
disabled, loading, success and error. Forms validate inline and say what to fix;
never use a browser alert. A newsletter or booking form ends in a clear success
state that says what happens next. See [interaction](../better-interaction.md) and
[accessibility](../better-accessibility.md).

## Ship requirements

| Area | Include |
| --- | --- |
| Metadata | Unique title, description, sharing image and tags, canonical URL, branded favicon and touch icon |
| Indexing | Index evergreen pages that match search intent; keep ad-only or short-lived campaign pages out of search when that is the intent. Decide deliberately, never by default |
| Answers | FAQ written as plain question and answer; structured data only when it matches visible content |
| Navigation | Current page indicated, a way back from every page, a branded 404 that leads somewhere useful |
| Links | No dead links; every action reaches its real destination or is visibly unavailable |
| Legal | Privacy and terms links, imprint where required, cookie consent only where tracking requires it, declining by default |
| Accessibility | Skip link, semantic landmarks, alt text on meaningful images, captions or transcripts for spoken video |
| Media | Sized and compressed images, reserved space, lazy loading below the fold, a poster for video, the opening content never delayed by secondary media |
| Local business | Address, hours, phone and map link as text; consistent with the business listing |
| Event | Date, time zone, venue and calendar link as text; ticket status kept current |

## Pitfalls

- Several competing actions in the opening.
- A vague value proposition: "streamline", "optimize", "reimagine".
- A long feature list with no outcomes.
- Proof buried at the bottom, or proof that cannot be verified.
- The product, work or place shown too small to read, or replaced by a stock image.
- Every section the same width and density, so nothing paces the read.
- Decoration everywhere, so the signature detail disappears.
- Content invisible until a reveal script runs.
- A mobile layout that breaks reading or pushes the action several screens down.
- No clear next step at the end.

## Before you finish

- [ ] The site type, narrative and one primary action are deliberate and stated.
- [ ] The first screen says what, whose and what next, at desktop and phone widths.
- [ ] Every claim, number, logo and quote is supplied or verified; gaps are marked.
- [ ] Proof sits beside the claims it supports.
- [ ] The primary action has one label, one destination and one distinct treatment,
      and appears again at the end.
- [ ] Sections vary in composition but share one visual system.
- [ ] One or two signature details, everything else quiet.
- [ ] Copy is readable and controls usable before and without any animation, and
      under reduced motion.
- [ ] Links, navigation and the conversion path work through success and failure;
      missing integrations are named.
- [ ] Metadata, favicon, sharing image, legal links and 404 are in place.
- [ ] Focus, contrast, zoom, loading and media sizing are checked on the rendered
      page.
- [ ] Any conversion claim is reported as a hypothesis until measured.
