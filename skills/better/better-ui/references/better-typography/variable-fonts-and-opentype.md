# Variable fonts and OpenType

What a font file can do beyond drawing letters and how to reach those abilities from CSS.

## Static vs variable

- **Static font:** one weight and one style per file. Regular, medium and bold is three files.
- **Variable font:** a whole range in one file. Any value in it works, such as `font-weight: 450`.

A variable font is not automatically better. At one or two weights, static files can be smaller. At several weights, optical sizes or custom axes, a variable font usually wins.

Intermediate weights such as `450` or `550` are a refinement, not something the hierarchy depends on. A static fallback rounds them to the nearest face it has, so two roles that differ only by `50` collapse into one.

## Disable synthesis narrowly

`font-synthesis: none` disables weight, style, small-cap and position synthesis together. Scope it to an isolated treatment you have verified across the fallback stack, such as a wordmark or an icon font:

```css
.brand-wordmark {
  font-synthesis: none;
}
```

To disable one mode, use its longhand: `font-synthesis-weight`, `font-synthesis-style`, `font-synthesis-small-caps` or `font-synthesis-position`.

## Axes

Variable-font controls, each with a four-letter tag. A font supports only the axes its designer included.

| Axis | Tag | Property | Controls |
| --- | --- | --- | --- |
| Weight | `wght` | `font-weight` | Stroke thickness |
| Optical size | `opsz` | `font-optical-sizing` | Details and spacing tuned for the rendered size |
| Width | `wdth` | `font-stretch` | Glyph width |
| Slant | `slnt` | `font-style: oblique 10deg` | Slant angle |
| Italic | `ital` | `font-style: italic` | True italic forms |
| Custom | e.g. `GRAD` (Roboto Flex) | none | Whatever the designer built |

Inter's variable file exposes only `wght` and `opsz`.

`font-optical-sizing: auto` is the default: sturdier strokes and looser spacing at small sizes, finer detail at large ones. Never set it to `none` as a fix. Optical sizes predate variable fonts, and many families still ship them as separate `Text` and `Display` files instead of an `opsz` axis.

## Raw tags only for custom axes

`font-variation-settings` silently does nothing on a non-variable fallback, where `font-weight` still picks the nearest face:

```css
/* Good: common axes use the properties */
.heading {
  font-weight: 650;
}

/* Good: custom axis with no property of its own */
.heading-grade {
  font-variation-settings: "GRAD" 80;
}

/* Bad: weight via raw tag breaks on fallback fonts */
.heading {
  font-variation-settings: "wght" 650;
}
```

## OpenType features

Unlike axes, features work the same on static and variable fonts. A font ships only the features its designer included.

| Tag | Feature | Default |
| --- | --- | --- |
| `kern` | Kerning: spacing for specific pairs like `AV` | On |
| `liga` | Standard ligatures: joins pairs like "fi" into one shape | On |
| `calt` | Contextual alternates: glyphs adapt to their neighbors, such as a colon raised between digits | On |
| `tnum` | Tabular numbers: every digit the same width | Off |
| `zero` | Slashed zero: `0` distinct from `O` | Off |
| `ss01`–`ss20` | Stylistic sets (numbered slots) | Off |
| `cv01`–`cv99` | Character variants (numbered slots) | Off |

Leave the defaults on. Turn ligatures off only on code, and only when the project prefers literal operators: `font-variant-ligatures: none`.

Common features have a `font-variant-*` property, and `font-feature-settings` is for the rest:

```css
/* Good: common features use the properties */
.price {
  font-variant-numeric: tabular-nums;
}

/* Good: niche feature with no property of its own */
.logo {
  font-feature-settings: "ss01" 1;
}
```

## Feature declarations replace, never merge

`font-feature-settings` is one value. A descendant that sets it drops every tag it inherited:

```css
.table { font-feature-settings: "tnum" 1; }
/* Bad: the ID column loses tabular numbers */
.table .id { font-feature-settings: "zero" 1; }
```

Two `font-variant-numeric` declarations on one element behave the same way: the later wins. List every value in one declaration instead, `font-variant-numeric: tabular-nums slashed-zero`. Tailwind's numeric utilities compose through CSS variables, so `tabular-nums slashed-zero` as classes already combine. When raw tags are unavoidable, repeat the inherited ones.

## Numeric variants

| Value | Use |
| --- | --- |
| `tabular-nums` | Changing values and numeric columns, so digits keep one width |
| `proportional-nums` | Figures in prose; the default in most fonts |
| `lining-nums` | Digits at cap height; tables, headings and interface text |
| `oldstyle-nums` | Digits that sit with lowercase; optional in long-form prose when the font ships them, never in UI or tables |
| `slashed-zero` | Codes, IDs, keys and anything retyped by hand |
| `diagonal-fractions` | Recipes and measurements: `1/2` renders as a true fraction |
| `ordinal` | Raised ordinal suffixes, as in `1st` |

A data column usually wants `lining-nums tabular-nums` in one declaration.

## Disambiguate characters people retype

Where a reader copies a value by eye, such as a code, a coupon, an API key or a serial, `I`, `l`, `1` and `0`, `O` must not collide. `slashed-zero` covers the zero. Many fonts collect the rest in a disambiguation stylistic set, such as Inter's `ss02`. Check the font's docs for the slot, and scope it to those values rather than the whole interface.

## Small caps, superscripts, subscripts

- **Small capitals:** uppercase letters drawn at a smaller size. Enable real ones with `font-variant-caps: small-caps`, and track them like uppercase labels.
- **Superscripts** sit above the normal line, the 2 in x², and **subscripts** below it, as in H₂O. Enable proper glyphs with `font-variant-position`.

Both require the font to include the glyphs; without them the browser synthesizes shrunken copies.

## Stylistic sets and character variants

`ss01` = stylistic set, slot 01. `cv11` = character variant, slot 11. What each slot does differs font to font, which is why they are numbered, not named. Check the font's docs. In Inter, `ss01` switches to open digits and `cv11` swaps in a single-story `a`.
