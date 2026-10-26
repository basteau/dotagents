# Declaration lookup

One-line lookup for every typography declaration this reference names. The rule lives
in the [top file](../better-typography.md); this is for finding the property.
Utility-class equivalents are in [tailwind](../tailwind.md).

## Font

| Declaration | What it does |
| --- | --- |
| `font-family: sans-serif` | The sans family |
| `font-family: serif` | The serif family |
| `font-family: monospace` | The monospace family |
| `font-size` | Size from the type scale |
| `font-size: clamp(…)` | Fluid display size with a `rem` term |
| `font-weight` | Any value from 1 to 1000 |
| `font-style: italic` | Switch to italic style |
| `font-stretch` | Width axis or width face |
| `-webkit-font-smoothing` + `-moz-osx-font-smoothing` | Smooth macOS font rendering; apply once at the root |
| `font-synthesis: none` | Disable all synthesized forms after verifying fallbacks and emphasis |
| `font-synthesis-weight: none` | Disable one synthesis mode |
| `font-display` | Text visibility while the face loads; set in `@font-face` |
| `font-feature-settings` | Toggle OpenType features; replaces inherited tags |
| `font-variation-settings` | Tune custom variable font axes |
| `font-optical-sizing: auto` | Optical size follows font size; the default, so leave it |
| `font-variant-ligatures: none` | Literal characters, code only |
| `font-variant-caps` | Real small capitals |
| `font-variant-position` | Real super and subscripts |

## Numbers

| Declaration | What it does |
| --- | --- |
| `font-variant-numeric: tabular-nums` | Equal-width digits |
| `font-variant-numeric: proportional-nums` | Natural-width digits |
| `font-variant-numeric: lining-nums` | Digits at cap height |
| `font-variant-numeric: oldstyle-nums` | Digits that sit with lowercase |
| `font-variant-numeric: slashed-zero` | Tell 0 from O |
| `font-variant-numeric: diagonal-fractions` | True fractions from `1/2` |
| `font-variant-numeric: ordinal` | Raised ordinal suffixes |

Plain CSS takes every numeric value in one declaration (`tabular-nums slashed-zero`).

## Spacing and layout

| Declaration | What it does |
| --- | --- |
| `letter-spacing` | Space between letters, in `em` |
| `line-height` | Space between lines, unitless |
| `font-kerning` | Kerning on or off; leave it on |
| `text-box: trim-both` | Trim space above and below |
| `max-width` on text columns | Cap at ~60–75 characters per line |
| `text-align` | Where lines start and end |

## Wrapping and overflow

| Declaration | What it does |
| --- | --- |
| `text-wrap: balance` | Even out heading lines |
| `text-wrap: pretty` | Keep a lone word off the last line |
| `text-overflow: ellipsis` | Ellipsis for clipped text |
| `overflow: hidden` + `text-overflow: ellipsis` + `white-space: nowrap` | Single-line truncation |
| `-webkit-line-clamp` + `display: -webkit-box` + `-webkit-box-orient: vertical` + `overflow: hidden` | Cut off after N lines |
| `overflow-wrap: break-word` | Break long strings |
| `white-space: nowrap` | Stop wrapping |
| `text-transform` | Change the casing |
| `hyphens: auto` | Hyphenate by the element's `lang` |

## Decoration and interaction

| Declaration | What it does |
| --- | --- |
| `text-decoration-line: underline` | Draw an underline |
| `text-decoration-color` | Underline color |
| `text-decoration-thickness` | Underline thickness |
| `text-underline-offset` | Push the line down |
| `text-underline-position: from-font` | Underline position from the font |
| `text-decoration-style` | Dotted, dashed or wavy |
| `text-decoration-thickness: from-font` | Underline set by the font |
| `text-decoration-skip-ink` | Gaps around descenders; `auto` is the default |
| `caret-color` | Tint the text cursor |
| `user-select: none` | Suppress selection on a verified drag/gesture conflict only |
| `text-shadow` | Shadow behind the letters |
| `-webkit-text-stroke` | Outline the letters |
| `background-clip: text` | Clip a background to the letters |
| `initial-letter` | Size a drop cap |
