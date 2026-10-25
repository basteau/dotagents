# Content, shipping and verification

What makes a landing page feel made rather than generated, and finished rather
than mocked up.

## Content realism

- Write real draft copy in the brand's voice. No lorem ipsum, and no placeholder
  brands, people or places ("Acme", "John Doe", "Lorem Café").
- Where the brief gives the shape of the content but not every detail (six rooms
  "from €140", a short menu, twelve speakers), write the full plausible set: each
  room with its name and rate, each dish with its price. A page can only be judged
  at its real density; list every sample detail for the client to confirm.
- Never present invented testimonials, logos, press, ratings or statistics as real.
  A specific-looking number ("47.2% faster") is as fictional as a round one; use a
  supplied figure, show the thing itself, or mark the gap: `[quote from a customer]`.
- For layout testing, use representative lengths: the longest name, a two-line
  title, a missing photo, an empty list. See [layout](../better-layout.md).
- Replace generic filler ("elevate", "seamless", "unleash", "streamline", "not
  just X, it's Y", "in today's world") with what the product does. Ask of each
  sentence whether a competitor could print it unchanged.
- Sentence case for headings, active voice, no exclamation marks in confirmations,
  no "Oops" in errors; see [writing](../better-writing.md).

## Honest imagery

Take pictures in this order, and report which you used:

| Source | Use it for | Keep it honest by |
| --- | --- | --- |
| The client's own photographs, footage, work and interface | Always first; an ordinary photo of the real thing beats a beautiful one of something else | Consented photos of real people; real screenshots shown large at least once |
| Generated images, when an image tool is available | Scenes the brief describes but no photo exists of | Never passing one off as the client's real product, room, dish or team |
| Open archives and museums | Its street in old postcards, a map of the neighbourhood, its material up close, its trade in engravings | A licence that allows commercial use, a credit line in the footer, never shown as the client's own |
| Drawn from the subject | Its objects, tiles, windows or interface when no honest picture exists | One graphic language in the palette's colors, with detail that rewards a second look; a drawing you cannot name in the subject's world is decoration |
| Type and color alone | When nothing above fits | Type as the image and color at full scale; no icons in squares, blobs or empty boxes |

Never a fake product shot, a stock stand-in for the subject, a device frame around
a shrunken screenshot or a gray box. Give every picture on the page one small set
of crop ratios and one treatment.

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

## Before you finish

- [ ] The site type, narrative and one primary action are deliberate and stated.
- [ ] The idea sentence passes the competitor test and reaches every section.
- [ ] The first screen says what, whose and what next, at desktop and phone widths.
- [ ] Every claim, number, logo and quote is supplied or verified; gaps are marked.
- [ ] Every section the brief asks for is delivered in full; sample details and
      found pictures with their credits are listed.
- [ ] Proof sits beside the claims it supports.
- [ ] The primary action has one label, one destination and one distinct treatment,
      and appears again at the end.
- [ ] Sections vary in composition but share one visual system.
- [ ] One or two signature details, quiet chrome, no thin or empty screens.
- [ ] Copy is readable and controls usable before and without any animation, and
      under reduced motion.
- [ ] Links, navigation and the conversion path work through success and failure;
      missing integrations are named.
- [ ] Metadata, favicon, sharing image, legal links and 404 are in place.
- [ ] Focus, contrast, zoom, loading and media sizing are checked on the rendered
      page.
- [ ] The page is critiqued from screenshots at about 1440, 768 and 390px, using
      [critique](../better-ui-review/critique.md).
- [ ] Any conversion claim is reported as a hypothesis until measured.
