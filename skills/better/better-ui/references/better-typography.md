# Typography

Typography is mostly restraint: a sensible scale, comfortable spacing, enough
contrast. A label, a table cell, a marketing headline and an article paragraph do not
share one set of rules. When reviewing, read the rendered page; bad wrapping, lone
last-line words and truncation only show up at real content lengths.

Use the project's styling system and its type tokens. Values here are starting points
tuned to the typeface, content and density, and verified at their rendered size. The
[declaration lookup](better-typography/css-cheat-sheet.md) lists every declaration
named here; utility equivalents are in [tailwind](tailwind.md).

The words belong to [better-writing](better-writing.md), semantic heading structure
to [better-accessibility](better-accessibility.md), spatial RTL layout and the way
back to truncated text to [better-layout](better-layout.md). Measure contrast with
[better-colors](better-colors.md); whether it passes is better-accessibility's.
Underline motion beyond color is [better-animation](better-animation.md)'s. This
reference owns how text renders, wraps and behaves in mixed-direction content.

## Measured, not preferred

Some values are exact: unitless line-height, weight `400` or heavier below `18px`, a
60–75 character measure, `16px` inputs on iOS and `tabular-nums` on changing values.
Missing them is a finding. Tracking, pairing and scale ratios are heuristics. Report
them only where they break the project's own scale. Never propose a new typeface,
paid or free, unless the task asks for a type change.

## Fonts and loading

Open [choosing-fonts.md](better-typography/choosing-fonts.md) for categories,
`Display` and `Text` cuts, fallback stacks and loading;
[variable-fonts-and-opentype.md](better-typography/variable-fonts-and-opentype.md)
for static versus variable files, axes, features, the synthesis longhands and why raw
tags [replace each
other](better-typography/variable-fonts-and-opentype.md#feature-declarations-replace-never-merge).

- **Fewer fonts, sizes and weights.** Default to one family per page and add a second
  only for a contrast you can name, such as a serif display over a sans body; two
  near-identical sans-serifs read as a mistake. Three families, a monospace for code
  included, is the ceiling for a whole system. Within a page, a few sizes far apart
  carry hierarchy better than many close steps. Below `18px`, use weight `400` or
  heavier; weights `100`–`300` belong at about `28px` and up.
- **Keep text visible while fonts load.** Serve `.woff2`. Text faces load with
  `font-display: swap`, or `optional` where a late swap would shift layout, never the
  invisible-text default, and a metric-matched fallback keeps the swap from moving
  the page. Keep the project's loader when it already does this.
- **Load the faces the design uses.** Browsers synthesize a weight or style the
  family doesn't ship, distorting the face. Disable synthesis only after verifying
  the fallback stack, and only the unwanted mode.
- **Properties over raw tags.** `font-weight: 650`, not `font-variation-settings:
  "wght" 650`; `font-variant-numeric: tabular-nums`, not `font-feature-settings:
  "tnum" 1`. Leave `font-optical-sizing` at `auto` and the default features on;
  reserve raw tags for custom axes like `"GRAD" 80` and features with no property,
  like `"ss01" 1`.

## Scale, spacing and size

Open [spacing-and-sizing.md](better-typography/spacing-and-sizing.md) for the
role-based starting scale, fluid display sizes, heading hierarchy, tracking values
and `text-box` trimming.

- **Use a type scale with semantic names.** Define a small set of sizes and pair each
  with its line-height and weight, so a role is one decision instead of three. Solo,
  default names like `text-sm` are fine when the usage rules are clear; on a team,
  name sizes by use (`text-body-sm`). Set `font-size` in `rem`; display text may
  scale through `clamp()` with a `rem` term, never `vw` alone.
- **Heading sizes descend with level**, so a subordinate heading never overpowers its
  parent. Deep levels may share a size where weight or tracking keeps them distinct.
  A heading is never smaller than body text unless it is a deliberate overline;
  choose the element for semantics, then size it in CSS.
- **Line-height by role:** display text `1.1`, headings `1.2`–`1.3`, body copy
  `1.5`–`1.6`, unitless so it scales with the font size. Anything that wraps to three
  or more lines needs at least `1.4`.
- **Tracking by size:** headings at `24px` and up take `-0.01em` to `-0.02em`,
  uppercase labels and small caps at `14px` or smaller about `0.05em`, body copy `0`,
  always in `em`. `font-kerning: none` is never a fix.
- **Size floors.** Start long-form body text at `16px`, moving off it only for a
  reason you can name, such as a typeface that runs small, a narrow measure or a
  dense professional tool. Start inputs and menus at `14px`, captions at `13px`, and
  rarely go below `12px`.
- **Trim text boxes in tight containers.** In buttons and badges, `text-box:
  trim-both cap alphabetic` stops text sitting low, as a progressive enhancement.

## Measure, wrapping and language

Open [wrapping-and-punctuation.md](better-typography/wrapping-and-punctuation.md) for
measure units, wrapping and justification, the punctuation substitution table and
internationalization.

- **Cap the measure** of long-form text at 60–75 characters per line, in any unit.
- **Wrap deliberately:** `text-wrap: balance` on headings and short descriptions,
  never paragraphs; `text-wrap: pretty` on any paragraph; `overflow-wrap: break-word`
  where a long word, URL or ID could escape its container; `white-space: nowrap` on
  labels and badges where a break looks broken. Interface text stays `text-align:
  start`; justify only editorial text, with `hyphens: auto` and a correct `lang`.
- **Natural case, typographic punctuation.** Store text in natural case and set case
  with `text-transform`, so a redesign never means rewriting copy. Rendered text uses
  typographic characters, never keyboard stand-ins; code keeps straight quotes.
- **Language and bidi behavior.** Set `lang` so browsers and assistive technology
  pick the right quotes, hyphenation and pronunciation, and `dir` on the document and
  wherever direction changes. Wrap a mixed-direction value in `<bdi>` and never
  reorder digits by hand.

## Numbers, truncation and details

Open the [numeric variants
table](better-typography/variable-fonts-and-opentype.md#numeric-variants) for
fractions, ordinals and disambiguation, and
[details-and-accessibility.md](better-typography/details-and-accessibility.md) for
underline recipes, selection, the two iOS input-zoom fixes and decorative text.

- **Numbers by context.** Timers, counters, prices and numeric columns take
  `tabular-nums`, so digits keep one width; figures in prose stay proportional.
  Codes, keys and IDs that people retype take `slashed-zero` and the font's
  disambiguation set.
- **Truncate with a way back.** One line: `overflow: hidden`, `text-overflow:
  ellipsis` and `white-space: nowrap`. Several: `display: -webkit-box`,
  `-webkit-box-orient: vertical`, `-webkit-line-clamp` and `overflow: hidden`. The
  way back to the full value is better-layout's.
- **Underlines from the font.** Set `text-underline-position: from-font` and
  `text-decoration-thickness: from-font`, or tune offset and thickness by hand, and
  keep `text-decoration-skip-ink` at `auto`. A dotted style hints at extra
  information, such as an abbreviation. Only the color of a real underline animates
  reliably.
- **Inputs at 16px on mobile.** iOS Safari zooms the page when a focused input's text
  is under `16px`. Two fixes hold `16px` and look different, so ask which the design
  wants: `16px` below the mobile breakpoint and the design size above it, or `16px`
  everywhere with the intended size rendered through `transform: scale()`.
- **Font smoothing on the root.** Apply `-webkit-font-smoothing: antialiased` and
  `-moz-osx-font-smoothing: grayscale` once on the root, never per component; both
  affect only macOS rendering, so judge the result there.
- **Decorative text in CSS, not images.** Drop caps, gradient text, outlines and text
  shadows built in CSS stay selectable, searchable and translatable.
- **Keep useful text selectable.** `::selection` can carry brand if the selected pair
  stays legible; `user-select: none` belongs only on a drag or gesture surface where
  accidental selection interferes.

## Before you finish

| Pattern | Fix |
| --- | --- |
| `font-weight` with no matching face in the loaded `@font-face` files | Load that face or use a weight the family ships |
| `@font-face` without `font-display`, or `block` on a text face | `swap`, or `optional` where layout shift matters |
| `font-synthesis: none` on body or interface text | Remove it; set only the unwanted longhand after checking fallbacks |
| `font-variation-settings: "wght"` or `"opsz"` | `font-weight`; drop `"opsz"` so `auto` applies |
| `font-feature-settings: "tnum"` or `"zero"` | `font-variant-numeric: tabular-nums` or `slashed-zero` |
| `font-feature-settings` on a descendant of another | Repeat the inherited tags, or move common ones to properties |
| `line-height` in `px` or `rem` | A unitless value |
| `line-height` under `1.4` on text that wraps to three or more lines | `1.4` or more |
| `letter-spacing` in `px` | The same value in `em` |
| Body `font-size` in `px`, or `font-size` in `vw` alone | `rem`; `clamp()` with a `rem` term for display text |
| Weight `100`–`300` on text under `18px` | `400` or heavier |
| Child heading renders larger than its parent | Remap that section to descending scale steps |
| `text-wrap: balance` on a paragraph | `text-wrap: pretty` |
| `text-align: justify` in UI, or without `hyphens: auto` | `text-align: start`; hyphenate editorial justify |
| `-webkit-line-clamp` without `display: -webkit-box` | Add `display: -webkit-box` and `-webkit-box-orient: vertical` |
| Timer, counter, price or numeric column without `tabular-nums` | `font-variant-numeric: tabular-nums` |
| `...`, `--` or a hyphenated range in rendered strings | `…`, an em dash or an en dash |
| `text-decoration-skip-ink: none` | Remove it; set `text-underline-offset` or `from-font` |
| `<input>` text under `16px` with no mobile override | One of the two [input-zoom fixes](better-typography/details-and-accessibility.md#ios-input-zoom) |
| `-webkit-font-smoothing` set inside a component | Move it to the root |
| Mixed-direction name, number or URL without `<bdi>` or `dir` | Wrap it in `<bdi>` |
| `user-select: none` on a layout root or text container | Remove it; keep it only on a drag or gesture surface |

## Reporting

**Severity.** Beyond the truncation and clipping [escalation
triggers](better-ui-review.md#rank-by-user-impact), `HIGH` makes text unreadable or
hides it while fonts load. `MEDIUM` breaks the type system or the visual heading
hierarchy. `LOW` is isolated polish.

**Verification.** Without a browser, check declared size and weight per heading level
within each section, line-height, units, measure, `@font-face` descriptors and
truncation against realistic strings. With one, compare computed values, throttle the
network to watch the font swap, and resize with real content to catch wrapping and
truncation. Report every check you could not run as `Not verified`.

For reviews, use [the shared review format](better-ui-review.md#report).
