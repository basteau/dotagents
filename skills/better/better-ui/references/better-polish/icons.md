# Icons

Icon stroke weight, sizing, state colors, variants and RTL mirroring. Animated icon
swaps and morphing belong to [better-animation](../better-animation.md), with recipes in
[icons-and-morphing.md](../better-animation/icons-and-morphing.md).

## Match icon stroke to text weight

A hairline icon beside semibold text reads as broken; a heavy icon beside regular
text shouts.

| Adjacent text | Rendered stroke width |
| --- | --- |
| Regular (400), 14–16px | `1.5px` |
| Medium or semibold (500–600) | `2px` |
| Bold (700), or an emphasized standalone icon | `2.5px` |

The widths are rendered pixels. A 24px-grid icon drawn at 16px scales its stroke by
16/24, so `stroke-width="2"` paints about `1.33px`. Use the library's absolute-stroke
option, such as Lucide's `absoluteStrokeWidth`, or set `vector-effect:
non-scaling-stroke`
on the SVG's shapes.

```css
/* Stroke tuned to the semibold label, in rendered pixels at any icon size */
.button { font-weight: 600; }
.button svg :is(path, line, circle, rect, polyline) {
  stroke-width: 2px;
  vector-effect: non-scaling-stroke;
}
```

- **One library per surface.** Never mix icon libraries with different stroke or corner
  conventions on one toolbar. Where the library has no stroke variants, keep its native
  stroke and use size or color for emphasis.
- **Size inline icons at `1em`–`1.25em`** of the adjacent font size, so the pair scales
  together.

## One SVG, recolored per state

Never ship separate assets for default, hover, selected and disabled. Draw every icon
with `currentColor` and let CSS state drive the color. Strip hardcoded fills such as
`fill="#666"` to `currentColor` when importing icons.

```html
<svg fill="none" stroke="currentColor" stroke-width="2" aria-hidden="true">…</svg>
```

```css
/* Use the project's color tokens */
.icon-button { color: var(--icon-muted); }
@media (hover: hover) { .icon-button:hover { color: var(--icon-strong); } }
.icon-button[aria-pressed="true"] { color: var(--icon-active); }
.icon-button:disabled { opacity: 0.4; }
```

Muted icons still carry meaning: an icon that is the only indicator of a control's
purpose needs `3:1` non-text contrast, measured per
[better-colors](../better-colors.md).

## Outline default, fill active

Where a set offers outline and filled variants, use them as a state pair, never
interchangeably.

| Variant | Use for |
| --- | --- |
| Outline | Default state in toolbars, list rows and inline text |
| Fill | Selected or active state, such as the active tab, a toggled bookmark or a liked heart |

Filled everywhere is the common miss: the active tab then has no state signal.

An infrequent swap, such as a bookmark or a like, may cross-fade; a high-frequency one,
such as the active tab, swaps instantly. Values are in
[icons-and-morphing.md](../better-animation/icons-and-morphing.md).

## Design at render size

- Test every icon at the smallest size it renders, often `16px`. It must stay
  recognizable there.
- Prefer a simplified glyph for small contexts over scaling down detailed artwork.
- Where the set ships per-size grids such as `16`, `20` and `24`, use the grid that
  matches the render size. A 24px drawing scaled to 16px lands off the pixel grid and
  renders soft.
- Always SVG, never raster.

## Icons in RTL

Under `dir="rtl"`, flip icons whose meaning follows reading direction and leave the
rest alone.

| Flip | Don't flip |
| --- | --- |
| Back and forward arrows, navigation chevrons | Logos and brand marks |
| Text-block glyphs for alignment, lists and indent | Checkmarks |
| Speaker and volume waves | Physical objects such as clocks, cups and pencils |
| Directional glyphs such as "send" | Media playback, which stays LTR by convention |

Use one mechanism per element. Two flips on one icon, say a class and a utility, can
compound back to unflipped.

```css
[dir="rtl"] .icon-directional {
  scale: -1 1;
}
```

Analyze composite icons part by part. A badge or slash overlay may keep its position
while the base glyph flips. Spatial RTL layout belongs to
[better-layout](../better-layout.md);
accessible names for icon-only buttons belong to
[better-accessibility](../better-accessibility.md).
