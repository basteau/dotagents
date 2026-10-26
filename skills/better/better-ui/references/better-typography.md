# Typography

Typography is mostly restraint: a sensible scale, comfortable spacing, enough
contrast, and separate rules for a label, a table cell, a headline and body copy.
Review rendered pages: wrapping, orphans and truncation show only at real lengths.

Use the project's styling system and type tokens. Values here are starting points,
tuned to the typeface, content and density and verified at their rendered size. The
[declaration lookup](better-typography/css-cheat-sheet.md) lists every declaration
named here; utility equivalents are in [tailwind](tailwind.md).

Words belong to [better-writing](better-writing.md), heading semantics and contrast
verdicts to [better-accessibility](better-accessibility.md), contrast measurement to
[better-colors](better-colors.md), RTL layout and truncation recovery to
[better-layout](better-layout.md), underline motion beyond color to
[better-animation](better-animation.md). This file owns rendering, wrapping and bidi.

## Measured, not preferred

Exact values: unitless line-height, weight `400`+ below `18px`, a 60–75 character
measure, `16px` inputs on iOS and `tabular-nums` on changing values. Missing them is
a finding. Tracking, pairing and scale ratios are heuristics: report only breaks of
the project's own scale. Never propose a new typeface unless asked for a type change.

## Fonts and loading

Open [choosing-fonts.md](better-typography/choosing-fonts.md) for categories,
`Display` and `Text` cuts, fallback stacks and loading, and
[variable-fonts-and-opentype.md](better-typography/variable-fonts-and-opentype.md)
for static or variable files, axes, features, synthesis longhands and [raw-tag
replacement](better-typography/variable-fonts-and-opentype.md#feature-declarations-replace-never-merge).

- **Fewer fonts, sizes and weights.** One family per page; add a second only for a
  contrast you can name, such as serif display over sans body, never two
  near-identical sans-serifs. Three families, code monospace included, is a system's
  ceiling. A few sizes far apart carry hierarchy better than many close steps. Below
  `18px`, use weight `400` or heavier; `100`–`300` belong at about `28px` and up.
- **Keep text visible while fonts load.** Serve `.woff2`. Text faces load with
  `font-display: swap`, or `optional` where a late swap shifts layout, never the
  invisible-text default, with a metric-matched fallback. Keep a loader that does
  this.
- **Load the faces the design uses.** Synthesized weights and styles distort the
  face. Disable only the unwanted synthesis mode, after verifying fallbacks.
- **Properties over raw tags.** `font-weight: 650`, not `font-variation-settings:
  "wght" 650`; `font-variant-numeric: tabular-nums`, not `font-feature-settings:
  "tnum" 1`. Leave `font-optical-sizing` at `auto` and default features on; keep raw
  tags for custom axes like `"GRAD" 80` and property-less features like `"ss01" 1`.

## Scale, spacing and size

Open [spacing-and-sizing.md](better-typography/spacing-and-sizing.md) for the
starting scale, fluid display, heading hierarchy, tracking and `text-box` trimming.

- **Use a type scale with semantic names.** Pair each of a few sizes with its
  line-height and weight, so a role is one decision, not three. Default names like
  `text-sm` suit solo work; teams name sizes by use (`text-body-sm`). Set `font-size`
  in `rem`; display text may use `clamp()` with a `rem` term, never `vw` alone.
- **Heading sizes descend with level**; deep levels may share a size where weight or
  tracking keeps them distinct. A heading is never smaller than body text unless it
  is a deliberate overline; choose the element for semantics, then size it in CSS.
- **Line-height by role:** display `1.1`, headings `1.2`–`1.3`, body `1.5`–`1.6`,
  unitless; anything wrapping to three or more lines needs at least `1.4`.
- **Tracking by size:** headings at `24px` and up `-0.01em` to `-0.02em`, uppercase
  labels and small caps at `14px` or smaller about `0.05em`, body `0`, always in
  `em`. `font-kerning: none` is never a fix.
- **Size floors.** Start long-form body at `16px`, leaving it only for a named
  reason: a typeface that runs small, a narrow measure or a dense professional tool.
  Start inputs and menus at `14px`, captions at `13px`, and rarely go below `12px`.
- **Trim text boxes in tight containers.** In buttons and badges, `text-box:
  trim-both cap alphabetic` stops text sitting low, as a progressive enhancement.

## Measure, wrapping and language

Open [wrapping-and-punctuation.md](better-typography/wrapping-and-punctuation.md) for
measure, wrapping, justification, punctuation substitutions and internationalization.

- **Cap the measure** of long-form text at 60–75 characters per line, in any unit.
- **Wrap deliberately:** `text-wrap: balance` on headings and short blurbs, never
  paragraphs; `pretty` on paragraphs; `overflow-wrap: break-word` where a URL or ID
  could escape; `white-space: nowrap` on labels and badges. UI text stays
  `text-align: start`; justify only editorial text, with `hyphens: auto` and `lang`.
- **Natural case, typographic punctuation.** Store text in natural case and set case
  with `text-transform`, so a redesign never means rewriting copy. Rendered text uses
  typographic characters, never keyboard stand-ins; code keeps straight quotes.
- **Language and bidi behavior.** Set `lang` so browsers and assistive tech pick
  quotes, hyphenation and pronunciation, and `dir` on the root and wherever direction
  changes. Wrap a mixed-direction value in `<bdi>`; never reorder digits by hand.

## Numbers, truncation and details

For fractions, ordinals and disambiguation, open the [numeric variants
table](better-typography/variable-fonts-and-opentype.md#numeric-variants); for
underline recipes, selection, the two iOS input-zoom fixes and decorative text,
[details-and-accessibility.md](better-typography/details-and-accessibility.md).

- **Numbers by context.** Timers, counters, prices and numeric columns take
  `tabular-nums`; figures in prose stay proportional. Codes, keys and IDs that people
  retype take `slashed-zero` and the font's disambiguation set.
- **Truncate with a way back.** One line: `overflow: hidden`, `text-overflow:
  ellipsis`, `white-space: nowrap`. Several: `display: -webkit-box`,
  `-webkit-box-orient: vertical`, `-webkit-line-clamp`, `overflow: hidden`.
- **Underlines from the font.** Set `text-underline-position` and
  `text-decoration-thickness` to `from-font` or tune them by hand; keep
  `text-decoration-skip-ink: auto`. Dotted hints at extra information (an
  abbreviation). Only a real underline's color animates reliably.
- **Inputs at 16px on mobile.** iOS Safari zooms when a focused input's text is under
  `16px`. Two fixes look different, so ask which: `16px` on mobile only, or `16px`
  everywhere scaled to the intended size with `transform: scale()`.
- **Font smoothing on the root.** Set `-webkit-font-smoothing: antialiased` and
  `-moz-osx-font-smoothing: grayscale` once, never per component (macOS only).
- **Decorative text in CSS, not images.** CSS drop caps, gradient text, outlines and
  shadows stay selectable, searchable and translatable.
- **Keep useful text selectable.** `::selection` can carry brand if the pair stays
  legible; `user-select: none` belongs only on a drag or gesture surface.

## Before you finish

| Pattern | Fix |
| --- | --- |
| `font-weight` with no matching loaded `@font-face` | Load that face or use a shipped weight |
| `@font-face` without `font-display`, or `block` on a text face | `swap`, or `optional` where layout shift matters |
| `font-synthesis: none` on body or interface text | Remove it; set only the unwanted longhand after checking fallbacks |
| `font-variation-settings: "wght"`/`"opsz"`, `font-feature-settings: "tnum"`/`"zero"` | `font-weight`, drop `"opsz"`; `tabular-nums` or `slashed-zero` |
| `font-feature-settings` on a descendant of another | Repeat the inherited tags, or move common ones to properties |
| `line-height` in `px` or `rem`, or under `1.4` on three-plus wrapped lines | Unitless, `1.4` or more on wrapping text |
| `letter-spacing` in `px`; body `font-size` in `px` or `vw` alone | `em`; `rem`, `clamp()` with a `rem` term for display |
| Weight `100`–`300` under `18px` | `400` or heavier |
| Child heading renders larger than its parent | Remap that section to descending scale steps |
| `text-wrap: balance` on a paragraph; `justify` in UI or without `hyphens: auto` | `pretty`; `start`, hyphenated editorial justify |
| `-webkit-line-clamp` without `display: -webkit-box` | Add it and `-webkit-box-orient: vertical` |
| Timer, counter, price or numeric column without `tabular-nums` | `font-variant-numeric: tabular-nums` |
| `...`, `--` or a hyphenated range in rendered strings | `…`, an em dash or an en dash |
| `text-decoration-skip-ink: none` | Remove it; set `text-underline-offset` or `from-font` |
| `<input>` text under `16px` with no mobile override | An [input-zoom fix](better-typography/details-and-accessibility.md#ios-input-zoom) |
| `-webkit-font-smoothing` set inside a component | Move it to the root |
| `user-select: none` on a layout root or text container | Keep it only on a drag or gesture surface |
| Mixed-direction name, number or URL without `<bdi>` or `dir` | Wrap it in `<bdi>` |

## Reporting

**Severity.** [Escalation triggers](better-ui-review.md#rank-by-user-impact) cover
truncation and clipping; `HIGH` makes text unreadable or hides it while fonts load,
`MEDIUM` breaks the type system or visual heading hierarchy, `LOW` is polish.

**Verification.** Without a browser, check heading sizes and weights per section,
line-height, units, measure, `@font-face` descriptors and truncation with realistic
strings. With one, compare computed values, throttle to watch the font swap, and
resize real content to catch wrapping. Unrun checks are `Not verified`.

For reviews, use [the shared review format](better-ui-review.md#report).
