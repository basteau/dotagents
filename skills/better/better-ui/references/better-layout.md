# Layout

Position, spacing and alignment carry hierarchy before a word is read. This reference
sets grouping, alignment, spacing and responsive structure, then stress-tests it:
resize it, zoom it, translate it, mirror it for RTL.

Write every fix in the project's styling system. The numbers below apply only where
the project has no spacing scale or density system; otherwise use its tokens, and
keep deliberate platform chrome and compact professional density that still pass the
stress tests. A finding needs a failure you can show, such as a clip, an overlap, a
broken mirror or a misread group. A value that differs from these starting points is
not one.

Hit areas and focus behavior belong to
[better-accessibility](better-accessibility.md). Radius, shadows and surfaces belong
to [better-polish](better-polish.md); motion belongs to
[better-animation](better-animation.md). Line length, text spacing and truncation
mechanics belong to [better-typography](better-typography.md); the words themselves
to [better-writing](better-writing.md). Viewport units, the software keyboard and
safe-area setup on mobile belong to [better-pwa](better-pwa.md); this reference owns
where content and controls sit inside them.

## Grouping, alignment and order

Open [grouping-and-alignment.md](better-layout/grouping-and-alignment.md) for
grouping recipes, control affordances, shared edges, the logical-property mapping
with RTL progression rules, and reading order.

- **Group with space, not lines.** Space groups first, background shapes second,
  separator lines last and only where space alone can't carry the structure. The gap
  between groups is at least 2× the gap within one, so `8px` inside a group means
  `16px` or more between groups.
- **Keep controls distinct from content.** Give every interactive element a
  background shape, a border, an underline or a consistent placement zone such as a
  toolbar. A control styled like the static text beside it does not read as a
  control, and a static badge shaped like a button collects dead clicks.
- **Align to shared edges.** Pick a small set of alignment edges and put everything
  on them; every stray edge reads as noise. Use one scale step per level of nesting,
  where `16px` is a useful default, and repeat it rather than inventing new indents.
- **Logical properties for anything that mirrors.** Write direction-dependent layout
  as leading and trailing: `margin-inline-start`, `padding-inline-end`,
  `inset-inline-start`, `text-align: start`. Reserve physical left and right for
  physical geometry such as safe-area insets.
- **Order by importance.** The most important content sits near the top and the
  leading edge. Within a row, identifying content leads; metadata and actions trail.
  Visual order matches DOM order, so never move content out of source sequence with
  `order`, `row-reverse` or grid placement.
- **One primary action per view.** Give each view one primary action. Once more than
  about three secondary actions compete, move the infrequent ones behind a labelled
  menu and keep frequent ones reachable. Prefer a short view that links deeper over
  one long view that shows everything at the top level.
  [better-colors](better-colors.md) owns how color marks the primary action.

## Spacing and adaptivity

Open [spacing-and-adaptivity.md](better-layout/spacing-and-adaptivity.md) for the
scale, control clearance, edge insets, hidden-content cues, bleed and safe-area code,
breakpoints and growth testing.

- **Space on one scale.** Take every gap, padding and margin from one spacing scale,
  so steps stay distinguishable and ratios stay clean. Arbitrary values such as
  `13px` beside `16px` read as mistakes, not intent. Without a project scale, start
  from a 4px base (`4, 8, 12, 16, 24, 32, 48`).
- **Borderless controls need more clearance.** Without an established density system,
  start with `12px` between adjacent bordered or filled controls and `24px` between
  the visible glyphs of borderless text and icon controls, counting each button's own
  padding. Compact layouts may use less, as long as hit areas never overlap and the
  controls stay distinct.
- **Inset buttons from the edges.** In content layouts, keep full-width buttons
  inside the layout margins, starting near `16px` inline on mobile. Edge-to-edge
  actions work when they follow established platform chrome, account for safe areas
  and stay distinguishable from system UI.
- **Content bleeds, controls float.** Backgrounds and media extend to the viewport
  edges. Controls and text stay inside the layout margins and safe areas, whose setup
  is [better-pwa](better-pwa.md#paint-edge-to-edge-pad-content-to-the-safe-area)'s.
  Content scrolls beneath sticky chrome. Set `scroll-padding-block-start` to the
  sticky header's height, so anchor targets and focused elements never land under it.
- **Hint at hidden content.** Every piece of off-screen or collapsed content needs a
  visible cue: the project's established one, the next item peeking `16–32px` past
  the scroll edge, or a disclosure control naming what is hidden. A truncated value
  the user needs has a path to the full text: an expand control, a detail view or a
  tooltip that also opens on keyboard focus.
- **Hold structure until it breaks.** Breakpoints come from the content, not device
  presets. Keep the expanded layout until it stops fitting, then collapse. Prefer
  container queries for component-level adaptation, and test the smallest and largest
  supported sizes first.
- **Plan for growth and clipping.** Translated strings grow, and short ones grow
  proportionally more, so a one-word button label is the riskiest thing on the
  screen. Size text containers with `max-width` and `min-height`, never a fixed width
  or height, and let rows wrap. Test with pseudo-localization and one long-string
  locale such as German. Never park a critical action where resizing, zoom, scrolling
  or the keyboard clips it. Keep it in the normal flow or in stable chrome.

## Before you finish

| Detection pattern | Fix |
| --- | --- |
| Spacing values off the project scale (`13px`, `22px`) | The nearest scale step that keeps the grouping ratio |
| Equal gaps inside and between groups, plus separator lines | Tighten within, widen between; drop the lines |
| `margin-left`, `padding-right`, `text-align: left` in a localizable layout | `margin-inline-start`, `padding-inline-end`, `text-align: start` |
| `float: left` on mirrored UI | `float: inline-start` |
| `translateX` or `background-position: left` on mirrored UI | A `[dir="rtl"]` override; neither has a logical form |
| `inset-inline-*` combined with `safe-area-inset-left` or `-right` | Physical `left` or `right` with the matching inset, plus a `[dir="rtl"]` override |
| `grid-template-columns: … 1fr` or a flex child with long text or a wide table, overflowing | `minmax(0, 1fr)` on the track, `min-width: 0` on the flex child |
| `width: 100vw` | `width: 100%`; `100vw` includes the scrollbar and scrolls horizontally on desktop |
| `height: 100vh` on a full-height mobile pane | `100dvh` or `100svh`, chosen per [better-pwa](better-pwa.md) |
| Fixed `height` or `width` on a box holding text | `min-height` or `max-width`, or `max-height` with `overflow-y: auto` |
| Breakpoints at `768px`/`1024px` because they are the defaults | Break where the content stops fitting |
| `@media (max-width: …)` inside a reusable component | `@container` on the component's parent |
| `container-type: inline-size` on a shrink-to-fit box (flex item with no width, inline-block, absolute) | Give it a definite width; size containment collapses it to zero |
| Sticky header with no `scroll-padding-block-start` | Set it to the header height |
| Truncated value with only a hover `title` | An expand control, detail view or focusable tooltip |

## Reporting

**Severity.** `HIGH` blocks content or an action at a supported viewport, as the
clipping, truncation and hidden-content [escalation
triggers](better-ui-review.md#rank-by-user-impact) describe; overlap at 320px or 200%
zoom counts too. `MEDIUM` harms hierarchy, reading order or adaptability. `LOW` is
isolated alignment or spacing polish.

**Verification.** Without a browser, check scale adherence, logical properties in
place of physical ones, container and media queries against the supported viewport
list, and DOM order against visual order. With one, check every supported width,
320px, 200% zoom, a long-string locale and the RTL mirror. Report every check you
could not run as `Not verified`.

For reviews, use [the shared review format](better-ui-review.md#report).
