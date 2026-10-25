# Exploring variants

Use this when the direction is genuinely open: a new piece, a redesign with several
defensible answers, or a user who wants to choose by seeing. Skip it for a fix, an
established pattern, or a direction already set. Variants are candidates, not a
review; never rank them for the user.

## Scope one piece

One piece per round: the metric card, not the dashboard. When a request spans
several, name the highest-leverage piece and offer the rest as later rounds. Restate
the brief in one sentence: what it is, where it renders, what it must do.

## Learn the ground

Read the stack, styling system, tokens, components, density and voice, and where the
piece renders: background, neighbors, widths. Variants share these; sharing tokens is
not convergence, and each variant should look shippable tomorrow. Product personality
bounds how far the boldest variant may go. With no project, use neutral grays, one
accent and the system font stack, and say so.

## Name the directions on one axis

Default to three variants; go to five only when asked. Pick one primary axis and give
each variant a different position on it. Secondary choices follow from that position
rather than varying on their own: a dense variant may need a smaller type step.

| Axis | What varies | Owner |
| --- | --- | --- |
| Structure | Grouping, order, column count, what collapses | [layout](../better-layout.md) |
| Density | Spacing scale, how much fits | [layout](../better-layout.md) |
| Emphasis | Where filled color goes, what recedes | [colors](../better-colors.md) |
| Type | Scale steps, weight contrast, measure | [typography](../better-typography.md) |
| Voice | Labels, tone, how much copy | [writing](../better-writing.md) |
| Behavior | Interaction model, what moves and how | [interaction](../better-interaction.md), [animation](../better-animation.md) |

Name each variant for its direction, such as Quiet, Editorial or Dense, never
Option A. Two variants that differ only in accent color or copy are one direction:
replace one or cut it. The set is ready when every variant has a name and a
phrase-length axis position and no two share one.

## Hold every variant to the same floor

Every variant fully works with real interactions and realistic content, and clears
the same floor: accessible names and visible focus, full keyboard reach, reduced
motion respected, no clipping at 320px width or 200% zoom, required contrast, no
meaning carried by color or motion alone. The floor is not an axis and never trades
against one; drop a direction that only works by breaking it. Criteria are in
[accessibility](../better-accessibility.md) and [review](../better-ui-review.md).

## Build in the real context

Host the variants on the page that will contain the piece, with its real chrome,
neighbors and item counts; with no such page, use one self-contained HTML file.
Render one variant at a time at full size, because thumbnails distort spacing and
spacing is usually what is being chosen. Switch instantly through a minimal selector
that sits visibly outside the design system; a URL parameter makes each variant
linkable. Keep the harness isolated so production code never imports from it.

## Compare rendered, then hand over

Flip through every variant in a browser: each renders, each interaction responds,
the console is clean. Judge at the widths that matter, since the answer can change
between 375px and 1440px. Without a browser, say so and hand over the URL or file.

Present the set and stop:

| Variant | Axis position | Right when | Cost |
| --- | --- | --- | --- |
| Quiet | Lowest visual weight | The page is used daily | Least memorable |
| Editorial | Largest type, most space | The moment deserves weight | Eats vertical space |

Mark no favorite. Asked to choose, answer from frequency of use and product
personality, not from which one was fun to build. If two variants converged while
building, cut one and say so.

## Promote one, remove the rest

Build the chosen variant properly where it belongs, in the project's conventions.
Then delete the other variants, the selector and the hosting page's imports, and
search for the variant names and parameter to confirm nothing remains. For another
round, keep the harness and diverge around the direction the user leaned toward.
