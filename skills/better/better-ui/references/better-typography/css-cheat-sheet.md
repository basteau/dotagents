# CSS cheat sheet

One-line lookup for every typography CSS declaration covered by this reference, with the Tailwind 4 equivalent. Where no utility exists, the arbitrary-value form is shown. Pick the column that matches the project: the declaration in plain CSS, CSS Modules, styled-components or StyleX codebases, the utility in Tailwind codebases.

## Font

| Declaration | What it does | Tailwind |
| --- | --- | --- |
| `font-family: sans-serif` | The sans family | `font-sans` |
| `font-family: serif` | The serif family | `font-serif` |
| `font-family: monospace` | The monospace family | `font-mono` |
| `font-size` | Size from the type scale | `text-*` |
| `font-size: clamp(…)` | Fluid display size with a `rem` term | `text-[clamp(2rem,1.25rem+3vw,3.5rem)]` |
| `font-weight` | Any value from 1 to 1000 | `font-*` |
| `font-style: italic` | Switch to italic style | `italic` |
| `font-stretch` | Width axis or width face | `font-stretch-*` |
| `-webkit-font-smoothing` + `-moz-osx-font-smoothing` | Smooth macOS font rendering; apply once at the root | `antialiased` |
| `font-synthesis: none` | Disable all synthesized forms after verifying fallbacks and emphasis | `[font-synthesis:none]` |
| `font-synthesis-weight: none` | Disable one synthesis mode | `[font-synthesis-weight:none]` |
| `font-display` | Text visibility while the face loads; set in `@font-face` | none, set by the loader |
| `font-feature-settings` | Toggle OpenType features; replaces inherited tags | `[font-feature-settings:"ss01"]` |
| `font-variation-settings` | Tune custom variable font axes | `[font-variation-settings:"GRAD"_80]` |
| `font-optical-sizing: auto` | Optical size follows font size; the default, so leave it | none needed |
| `font-variant-ligatures: none` | Literal characters, code only | `[font-variant-ligatures:none]` |
| `font-variant-caps` | Real small capitals | `[font-variant-caps:small-caps]` |
| `font-variant-position` | Real super and subscripts | `[font-variant-position:super]` |

## Numbers

| Declaration | What it does | Tailwind |
| --- | --- | --- |
| `font-variant-numeric: tabular-nums` | Equal-width digits | `tabular-nums` |
| `font-variant-numeric: proportional-nums` | Natural-width digits | `proportional-nums` |
| `font-variant-numeric: lining-nums` | Digits at cap height | `lining-nums` |
| `font-variant-numeric: oldstyle-nums` | Digits that sit with lowercase | `oldstyle-nums` |
| `font-variant-numeric: slashed-zero` | Tell 0 from O | `slashed-zero` |
| `font-variant-numeric: diagonal-fractions` | True fractions from `1/2` | `diagonal-fractions` |
| `font-variant-numeric: ordinal` | Raised ordinal suffixes | `ordinal` |

Plain CSS takes every numeric value in one declaration; Tailwind's numeric utilities compose.

## Spacing and layout

| Declaration | What it does | Tailwind |
| --- | --- | --- |
| `letter-spacing` | Space between letters, in `em` | `tracking-*` |
| `line-height` | Space between lines, unitless | `leading-*` |
| `font-kerning` | Kerning on or off; leave it on | `[font-kerning:none]` |
| `text-box: trim-both` | Trim space above and below | `[text-box:trim-both_cap_alphabetic]` |
| `max-width` on text columns | Cap at ~60–75 characters per line | `max-w-xl` / `max-w-2xl` / `max-w-prose` |
| `text-align` | Where lines start and end | `text-start` / `text-center` / `text-justify` |

## Wrapping and overflow

| Declaration | What it does | Tailwind |
| --- | --- | --- |
| `text-wrap: balance` | Even out heading lines | `text-balance` |
| `text-wrap: pretty` | Keep a lone word off the last line | `text-pretty` |
| `text-overflow: ellipsis` | Ellipsis for clipped text | `text-ellipsis` |
| `overflow: hidden` + `text-overflow: ellipsis` + `white-space: nowrap` | Single-line truncation | `truncate` |
| `-webkit-line-clamp` + `display: -webkit-box` + `-webkit-box-orient: vertical` + `overflow: hidden` | Cut off after N lines | `line-clamp-*` |
| `overflow-wrap: break-word` | Break long strings | `break-words` |
| `white-space: nowrap` | Stop wrapping | `whitespace-nowrap` |
| `text-transform` | Change the casing | `uppercase` / `capitalize` |
| `hyphens: auto` | Hyphenate by the element's `lang` | `hyphens-auto` |

## Decoration and interaction

| Declaration | What it does | Tailwind |
| --- | --- | --- |
| `text-decoration-line: underline` | Draw an underline | `underline` |
| `text-decoration-color` | Underline color | `decoration-*` |
| `text-decoration-thickness` | Underline thickness | `decoration-1` / `decoration-2` |
| `text-underline-offset` | Push the line down | `underline-offset-*` |
| `text-underline-position: from-font` | Underline position from the font | `[text-underline-position:from-font]` |
| `text-decoration-style` | Dotted, dashed or wavy | `decoration-dotted` / `decoration-wavy` |
| `text-decoration-thickness: from-font` | Underline set by the font | `decoration-from-font` |
| `text-decoration-skip-ink` | Gaps around descenders; `auto` is the default | `[text-decoration-skip-ink:auto]` |
| `caret-color` | Tint the text cursor | `caret-*` |
| `user-select: none` | Suppress selection on a verified drag/gesture conflict only | `select-none` |
| `text-shadow` | Shadow behind the letters | `text-shadow-*` |
| `-webkit-text-stroke` | Outline the letters | `[-webkit-text-stroke:1px_black]` |
| `background-clip: text` | Clip a background to the letters | `bg-clip-text` |
| `initial-letter` | Size a drop cap | `[initial-letter:3]` |
