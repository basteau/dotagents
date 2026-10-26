# Contrast

Contrast is measured between a **foreground color**, meaning text, an icon or a UI
element, and the **background color** it actually renders against, usually the
nearest ancestor that paints one. Identify that background first. Measuring against
the page background when the element sits on a card gives the wrong answer.

[better-accessibility](../better-accessibility.md) decides which requirement applies
to a pair and whether it must pass. This file covers measuring the pair, reporting it
and correcting it when implementation is in scope. Where neither that reference nor
the project names a standard, report the WCAG 2 result and add the APCA value.

**Match the task.** In a review, report the failing pair, its measured value and the
threshold it misses. In a build or requested improvement, correct failures within
scope while preserving the intended palette, then remeasure.

## WCAG 2 ratios

WCAG 2 is the standard formal conformance claims are made against. Its AA criteria,
including the large-text size and the exemptions, are listed in
[better-accessibility](../better-accessibility.md#pair-color-with-a-second-cue):
4.5:1 for text, 3:1 for large text and for UI components and meaningful graphics.
Where a project targets AAA, text needs 7:1 and large text 4.5:1.

## APCA values

Lc, Lightness Contrast, is APCA's measure of perceived contrast. It models polarity
and font weight better than the WCAG 2 ratio, which makes it the better tiebreaker
for design decisions above the WCAG gate. These levels simplify APCA's full font-size
and weight lookup table:

| Content type | Minimum | Preferred |
| --- | --- | --- |
| Body text, columns or blocks of text | Lc 75 | Lc 90 |
| Non-body text such as labels and headlines | Lc 60 | Lc 75 |
| Large text, at least `36px` regular or `24px` bold | Lc 45 | Lc 60 |
| Fine-detail or outline icons | Lc 45 | n/a |
| Solid icons and UI components | Lc 30 | n/a |
| Non-text that only has to be discernible, such as dividers | Lc 15 | n/a |

Disabled and placeholder text are not covered by a requirement. Aim for Lc 30 so they
stay legible.

Lc is signed. Positive means dark text on a light background, negative means light
text on a dark background. Compare the absolute value against the threshold.

## Fixing a failing pair

**Change lightness first.** It is the channel contrast responds to. Hue and chroma
move the measured value far less, so a contrast fix that changes hue is wasted effort
and a palette change besides.

Move the foreground away from the background in OKLCH `L`, holding `C` and `H`, then
remeasure. In OKLCH this is a one-channel edit; in hex, convert, edit `L` and convert
back.

```css
/* Failing: text too close to its background in lightness (Lc ≈ 50, 2.8:1) */
color: #7d93b0;      /* oklch(0.656 0.05 255) */
background: #eef2f7; /* oklch(0.96 0.008 254) */

/* Fixed: darker text, same hue (Lc ≈ 88, 10.3:1) */
color: #2b3a4f;      /* oklch(0.345 0.042 257) */
background: #eef2f7;
```

Two constraints on the fix:

- **Mid-lightness backgrounds cap what is achievable.** On a background near `L`
  `0.75`, even pure black text reaches only about Lc 60. Body text needs a background
  near one extreme, so a mid-range background is the thing that has to change.
- **Pushing lightness can push the color out of gamut.** Reduce chroma as needed to
  keep it renderable. See [color-formats.md](color-formats.md#gamut).

Always remeasure after changing a value. Do not assume a fix landed.

## First-pass lightness gaps

For choosing a starting value only. These were computed for neutral pairs; chroma
moves them slightly, so measure before reporting anything.

| Background `L` | Body text at WCAG 4.5:1 | Body text at APCA Lc 75 |
| --- | --- | --- |
| `0.97` | `L` at most `0.54` | `L` at most `0.49` |
| `0.90` | `L` at most `0.49` | `L` at most `0.36` |
| `0.20` | `L` at least `0.60` | `L` at least `0.85` |

The gaps are asymmetric and the two metrics disagree, most of all on dark
backgrounds. A light-on-dark pair that clears 4.5:1 can sit well short of Lc 75.

## Pick text polarity from the background

Compare black and white text using the project's required metric. The two metrics
cross over in different places, so they can favor different text colors on the same
background, and a higher score does not by itself mean the pair passes.

| Metric | Neutral crossover | Above it |
| --- | --- | --- |
| WCAG 2 | `L` near `0.56` | Black text scores higher |
| APCA | `L` near `0.73` | Black text scores higher |

Between the two, the metrics disagree. At `oklch(0.65 0 0)`, black measures `6.49:1`
and white `3.23:1`, so only black passes WCAG AA for normal text, while APCA scores
white higher, Lc `65` against black's `44`. For colored backgrounds or other
foregrounds, measure the actual pair rather than applying a crossover.

## What to check

- **Every pair, in every appearance.** APCA scores mirrored pairs differently, so a
  pair passing in light mode can fail in dark.
- **Translucent surfaces.** A color on a `backdrop-filter` header or an overlay
  shifts with whatever scrolls behind it. Test against the lightest and darkest
  content it can sit over, or make the surface opaque enough that the shift cannot
  break the pair.
- **Computed colors.** `color-mix()`, relative color syntax, `light-dark()` and alpha
  modifiers on utility classes (such as `/50`) resolve at render time; measure the
  rendered result, not the declaration.
- **Text over images.** There is no single background color. Measure the worst
  region, or guarantee one with a scrim.
