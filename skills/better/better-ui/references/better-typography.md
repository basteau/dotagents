# Typography

Typography is mostly restraint: a sensible scale, comfortable spacing, enough contrast. A label, a table cell, a marketing headline and an article paragraph do not share one set of rules. When reviewing, read the rendered page; bad wrapping, lone last-line words and truncation only show up at real content lengths.

Use the project's styling system and its type tokens. Values here are starting points tuned to the typeface, content and density, and verified at their rendered size. The [cheat sheet](better-typography/css-cheat-sheet.md) maps each declaration to its Tailwind utility.

The words belong to [better-writing](better-writing.md), semantic heading structure to [better-accessibility](better-accessibility.md), spatial RTL layout and the way back to truncated text to [better-layout](better-layout.md). Measure contrast with [better-colors](better-colors.md); whether it passes is better-accessibility's. Underline motion beyond color is [better-animation](better-animation.md)'s. This reference owns how text renders, wraps and behaves in mixed-direction content.

## Measured, not preferred

Some values are exact: unitless line-height, weight `400` or heavier below `18px`, a 60–75 character measure, `16px` inputs on iOS and `tabular-nums` on changing values. Missing them is a finding.

Tracking, pairing and scale ratios are heuristics. Report them only where they break the project's own scale. Never propose a new typeface, paid or free, unless the task asks for a type change.

## Fewer fonts, sizes and weights

Rarely use more than three fonts; marketing pages can carry more than apps. Weight and size carry hierarchy, so each extra one dilutes it. Pair for contrast: a serif headline over a sans body reads as deliberate, two near-identical sans-serifs read as a mistake.

Below `18px`, use weight `400` or heavier. Weights `100`–`300` belong at `28px` and up, checked against the background even there. Categories, `Display` and `Text` cuts and fallback stacks are in [choosing-fonts.md](better-typography/choosing-fonts.md).

## Keep text visible while fonts load

Serve `.woff2`. Text faces load with `font-display: swap`, or `optional` where a late swap would shift layout, never the invisible-text default. A metric-matched fallback keeps the swap from moving the page. Keep the project's loader when it already does this; see [loading](better-typography/choosing-fonts.md#loading).

## Load the faces the design uses

Browsers synthesize a weight or style the family doesn't ship, distorting the face. Load every face the design uses. Disable synthesis only after verifying the fallback stack, and only the unwanted mode, with the [synthesis longhands](better-typography/variable-fonts-and-opentype.md#disable-synthesis-narrowly).

## Properties over raw tags

When a CSS property exists, use it: `font-weight: 650`, not `font-variation-settings: "wght" 650`; `font-variant-numeric: tabular-nums`, not `font-feature-settings: "tnum" 1`. Properties survive a non-variable fallback, and they combine where raw tags [replace each other](better-typography/variable-fonts-and-opentype.md#feature-declarations-replace-never-merge).

Leave `font-optical-sizing` at its default `auto` and the default features (`kern`, `liga`, `calt`) on. Reserve raw tags for custom axes like `"GRAD" 80` and features with no property, like `"ss01" 1`. Axes, features and static versus variable files are in [variable-fonts-and-opentype.md](better-typography/variable-fonts-and-opentype.md).

## Use a type scale with semantic names

Define a small set of sizes and deviate from it as little as possible. Pair each size with its line-height and weight, so a role is one decision instead of three. Solo, default names like `text-sm` are fine when the usage rules are clear; on a team, name sizes by use (`text-body-sm`). See the [role-based starting scale](better-typography/spacing-and-sizing.md#type-scale).

Set `font-size` in `rem` so the reader's browser font size applies. Display text may scale with the viewport through `clamp()` with a `rem` term, never `vw` alone; see [fluid display sizes](better-typography/spacing-and-sizing.md#fluid-display-sizes).

## Heading sizes descend with level

Map heading levels to descending steps of the scale, so a subordinate heading never overpowers its parent. Deep levels may share a size where the scale runs out, as long as weight or tracking keeps them distinct. A heading is never smaller than body text unless it is a deliberate overline. Choose the element for semantics, then size it in CSS.

## Line-height by role

Display text `1.1`, headings `1.2`–`1.3`, body copy `1.5`–`1.6`. Use unitless values so line-height scales with the font size; a fixed `24px` does not. Anything that wraps to three or more lines needs at least `1.4`, even in a height-constrained row.

## Tracking by size

Headings at `24px` and up take `-0.01em` to `-0.02em`. Uppercase labels and small caps at `14px` or smaller take about `0.05em`. Body copy stays at `0`. Use `em` so tracking scales with the size. Kerning is on by default; `font-kerning: none` is never a fix. See [tracking by size](better-typography/spacing-and-sizing.md#tracking-by-size).

## Cap the measure

Cap long-form text at 60–75 characters per line. Any unit works, as long as a cap exists and the line length lands in range. See [unit choices and pixel equivalents](better-typography/wrapping-and-punctuation.md#measure-line-length).

## Wrap deliberately

- `text-wrap: balance` on headings and short descriptions, never paragraphs.
- `text-wrap: pretty` keeps a lone word off the last line, on any paragraph.
- `overflow-wrap: break-word` where a long word, URL or ID could escape its container.
- `white-space: nowrap` on labels and badges where a break looks broken.

Interface text stays `text-align: start`. Justify only editorial text, with `hyphens: auto` and a correct `lang`. See [wrapping and justification](better-typography/wrapping-and-punctuation.md#wrapping).

## Numbers by context

Timers, counters, prices and numeric columns take `tabular-nums`, so digits keep one width and nothing shifts. Figures in prose stay proportional. Codes, keys and IDs that people retype take `slashed-zero` and the font's disambiguation set. Fractions and ordinals have their own values in the [numeric variants table](better-typography/variable-fonts-and-opentype.md#numeric-variants).

## Truncate with a way back

One line: `overflow: hidden`, `text-overflow: ellipsis` and `white-space: nowrap` (Tailwind `truncate`). Several: `display: -webkit-box`, `-webkit-box-orient: vertical`, `-webkit-line-clamp` and `overflow: hidden` (Tailwind `line-clamp-*`). Keep the full value reachable; the affordance is better-layout's.

## Natural case, typographic punctuation

Store text in natural case and set case with `text-transform`, so a redesign never means rewriting copy. Rendered text uses typographic characters, never keyboard stand-ins; see the [substitution table](better-typography/wrapping-and-punctuation.md#smart-punctuation). Code keeps straight quotes.

## Underlines from the font

Set `text-underline-position: from-font` and `text-decoration-thickness: from-font`, or tune `text-underline-offset` and thickness by hand. Keep `text-decoration-skip-ink` at `auto`. A dotted `text-decoration-style` hints at extra information, such as an abbreviation. Only the color of a real underline animates reliably; anything else needs a separate element. See the [underline recipes](better-typography/details-and-accessibility.md#underlines).

## Inputs at 16px on mobile

iOS Safari zooms the page when an input's text is under `16px`. Two fixes hold `16px` and look different, so ask which the design wants: size the input up on mobile (`text-base sm:text-sm`), or keep `16px` and render the intended size with `transform: scale()`. Both recipes are in [forms and editable text](better-typography/details-and-accessibility.md#forms-and-editable-text).

## Size floors

Start long-form body text at `16px`. Move off it only for a reason you can name, such as a typeface that runs small, a narrow measure or a dense professional tool. Start inputs and menus at `14px`, captions at `13px`, and rarely go below `12px`.

## Font smoothing on the root

Apply `-webkit-font-smoothing: antialiased` and `-moz-osx-font-smoothing: grayscale` once on the root, never per component. Tailwind's `antialiased` sets both.

## Language and bidi behavior

Set `lang` so browsers and assistive technology pick the right quotes, hyphenation and pronunciation. Set `dir` on the document and wherever direction changes. Wrap a mixed-direction value in `<bdi>` and never reorder digits by hand. See [internationalization](better-typography/wrapping-and-punctuation.md#internationalization).

## Trim text boxes in tight containers

In buttons and badges, the space a font reserves above and below its letters makes text sit low. `text-box: trim-both cap alphabetic` trims it as a progressive enhancement. See [text-box](better-typography/spacing-and-sizing.md#text-trimming-with-text-box).

## Decorative text in CSS, not images

Build drop caps, gradient text, outlines and text shadows in CSS so the text stays selectable, searchable and translatable. See [decorative text](better-typography/details-and-accessibility.md#decorative-text).

## Keep useful text selectable

`::selection` can carry brand into reading, as long as the selected pair stays legible. `user-select: none` belongs only on a drag or gesture surface where accidental selection interferes, never across the interface. Other ranges are in [selection](better-typography/details-and-accessibility.md#selection).

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
| `leading-none` or `leading-tight` on text that can wrap | `1.4` or more |
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
| `<input>` with `text-sm` or `14px` and no mobile override | One of the **Inputs at 16px on mobile** fixes |
| `antialiased` or `-webkit-font-smoothing` inside a component | Move it to the root |
| Mixed-direction name, number or URL without `<bdi>` or `dir` | Wrap it in `<bdi>` |
| `user-select: none` on a layout root or text container | Remove it; keep it only on a drag or gesture surface |

## Reporting

**Severity.** `HIGH` makes text unreadable, hides text while fonts load, truncates content with no way back, or clips text at 320px width or 200% zoom. `MEDIUM` breaks the type system or the visual heading hierarchy. `LOW` is isolated polish.

**Verification.** Without a browser, check declared size and weight per heading level within each section, line-height, units, measure, `@font-face` descriptors and truncation against realistic strings. With one, compare computed values, throttle the network to watch the font swap, and resize with real content to catch wrapping and truncation. Report every check you could not run as `Not verified`.

For reviews, use the shared format in [better-ui-review](better-ui-review.md).
