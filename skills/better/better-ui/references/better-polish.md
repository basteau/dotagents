# UI polish

Small visual details compound: nested radii, optical alignment, elevation, edges,
decorative layers and icons. Polish applies once the interaction is sound; a polish
finding never outranks a broken interaction. Keep the project's component library,
tokens and density. Values here are starting points tuned by eye, and a deliberate,
consistent project convention (a flat style with no shadows) is a preference, not a
finding. The same detail done two ways within the project is a finding.

Motion of any kind, including hover transitions, icon swaps and view-transition
timing, belongs to [better-animation](better-animation.md). Hit areas, focus rings,
forced colors and reduced motion belong to
[better-accessibility](better-accessibility.md).
Text rendering and `::first-line` belong to [better-typography](better-typography.md);
grouping and the spacing scale belong to [better-layout](better-layout.md); color
tokens and contrast measurement belong to [better-colors](better-colors.md).

## Nest radii concentrically

Where nested surfaces share a visible, even inset, outer radius = inner radius +
padding + border width. Past about `24px` of padding, or with deliberately asymmetric
padding, treat the layers as separate surfaces and keep each one's radius token. See
[surfaces.md](better-polish/surfaces.md#concentric-radius).

## Align optically where geometry looks off

Judge at render size before nudging, since the icon set may already compensate. Start
an icon-side button padding `2px` under the text side, shift a play triangle toward
its point, and fix asymmetric glyphs in the SVG rather than in the component. See
[surfaces.md](better-polish/surfaces.md#optical-alignment).

## Shadows for elevation, borders for structure

Where a border exists only to fake depth, replace it with a layered transparent
`box-shadow` ring, which adapts to any background. Keep borders on dividers, table
cells, selected states and form inputs, whose boundary needs measured non-text
contrast. Forced-colors mode drops every shadow, so keep `border: 1px solid
transparent` under a shadow ring. See
[surfaces.md](better-polish/surfaces.md#shadow-rings).

## Build elevation as a scale

Define a few shadow tokens and assign them by depth: surfaces at one level share a
token, and higher overlays get larger offset and blur. Layer two or three shadows of
rising blur and falling opacity instead of one heavy blur, keep every offset pointing
the same way to imply one light source, and keep alpha low rather than reaching for
dark, opaque black. In dark mode, carry elevation with lighter surface steps and a
faint light ring. See [surfaces.md](better-polish/surfaces.md#elevation-scale).

## Separate with alpha, not fixed grays

Structural borders and separators use a translucent neutral token so they hold up
over tinted sections, images and both themes. Measure any boundary that must meet
contrast against the background it actually renders on. See
[surfaces.md](better-polish/surfaces.md#alpha-borders).

## Outline images in neutral alpha

Give content photos a `1px` outline inset by `1px`, in pure black at low alpha on
light surfaces and pure white at low alpha on dark ones. Never a tinted near-black or
the accent, which reads as a colored fringe. Skip transparent artwork such as logos
and illustrations. See [surfaces.md](better-polish/surfaces.md#image-outlines).

## Layer tactile buttons deliberately

A convincing raised button is several quiet layers: a hairline cut shadow, inner
highlights, stacked depth shadows and a gradient too subtle to notice. Use the full
anatomy only where the product's style has depth; flat systems stay flat. See
[surfaces.md](better-polish/surfaces.md#tactile-button-anatomy).

## Decorate with pseudo-elements, not extra nodes

Hover backgrounds, glows, overlays, elevated shadows and hit-area extensions belong
on `::before` and `::after`, not on empty `<span>`s. They need `content`, a
positioned parent, a stacking context and usually `pointer-events: none`. Use a real
element when the content is meaningful, interactive or driven independently. See
[pseudo-elements.md](better-polish/pseudo-elements.md).

## Style native parts through their pseudo-elements

Restyle `::backdrop`, `::placeholder`, `::selection`, `::marker` and
`::file-selector-button` instead of rebuilding them with wrapper nodes, overlays or
background-image bullets. See
[pseudo-elements.md](better-polish/pseudo-elements.md#native-pseudo-elements).

## Use view transitions for one-shot view changes

For route changes, DOM swaps and shared-element moves, prefer the View Transitions
API: name the moving elements uniquely, remove the names afterward, and style the
`::view-transition-*` pseudo-elements. Keep the existing motion library for
interruptible, gesture-driven or in-component animation, because a view transition
cannot be retargeted mid-flight. Mechanics are in
[pseudo-elements.md](better-polish/pseudo-elements.md#view-transitions); timing and
reduced motion are in [better-animation](better-animation.md).

## Match icon stroke to text weight

An icon's rendered stroke tracks the adjacent text, from about `1.5px` at 400 to
`2.5px` at 700, measured in rendered pixels after scaling. Keep one icon library per
surface and preserve its native conventions where it has no stroke variants. See
[icons.md](better-polish/icons.md).

## One SVG, recolored per state

Icons draw with `currentColor` and take hover, selected and disabled states from CSS
color and opacity, never from separate assets. Outline is the default variant and
fill marks the active state; under `dir="rtl"`, mirror only icons whose meaning
follows reading direction. See
[icons.md](better-polish/icons.md#one-svg-recolored-per-state).

## Before you finish

| Detection | Fix |
| --- | --- |
| Padded parent and child with the same radius value or utility | Add padding and border width to the outer radius |
| `box-shadow: 0 0 0 1px` ring with no `border` beside it | Add `border: 1px solid transparent` |
| One heavy single-layer shadow, or offsets pointing different ways | Layered shadows from the elevation scale, one light direction |
| Hardcoded gray border (`#e5e5e5`) over tinted or image backgrounds | Alpha neutral border token |
| Tinted or hex `outline` color on a content `img` | Pure black or white at low alpha |
| Empty decorative `<span>` or overlay `<div>` | `::before` or `::after` |
| `::before`/`::after` without `content`, or on `img`/`input` | Add `content: ""`; move it to a wrapper |
| `view-transition-name` in a rule that matches many elements | Unique names set only on the moving elements |
| `fill="#..."` or `stroke="#..."` inside an icon SVG | `currentColor` |
| Two icon libraries on one toolbar | One library per surface |

## Reporting

**Severity.** A visible inconsistency in surfaces or icons is `MEDIUM`; isolated
polish is `LOW`.

**Verification.** Read every state the component defines (hover, pressed, selected,
disabled, dark theme, forced colors) and inspect rendered surfaces on their real
backgrounds. Report every check you could not run as `Not verified`.

For reviews, use [the shared review format](better-ui-review.md#report).
