# Surfaces

Recipes for concentric radius, optical alignment, shadow rings, elevation, alpha
borders, image outlines and tactile buttons. Translate values into the project's
tokens; the numbers are starting points. Transition timing belongs to
[better-animation](../better-animation.md).

## Concentric radius

```
outerRadius = innerRadius + padding + borderWidth
```

Derive the outer radius from tokens so the pair stays concentric when either changes:

```css
.card {
  --inset: 8px;
  --inner-radius: 12px;
  padding: var(--inset);
  border-radius: calc(var(--inner-radius) + var(--inset)); /* 20px */
}
.card-inner {
  border-radius: var(--inner-radius);
}
```

```tsx
// Tailwind: 16px outer radius, 8px padding, 8px inner radius
<div className="rounded-2xl p-2">
  <div className="rounded-lg">...</div>
</div>
// Bad: rounded-xl on both, so the inner corners look pinched
```

Past about `24px` of padding the curves no longer read as related; choose each radius
independently. Where the padding is deliberately asymmetric, keep the component's tokens.

## Optical alignment

### Buttons with text and an icon

Start the icon side at `2px` less padding than the text side, then judge by eye.

```css
.button-with-icon {
  padding-inline-start: 16px;
  padding-inline-end: 14px; /* trailing icon side */
}
```

```tsx
<button className="ps-4 pe-3.5 flex items-center gap-2">
  <span>Continue</span>
  <ArrowRightIcon />
</button>
```

### Play triangles

A triangle's geometric center sits behind its visual center, so shift it toward its point:

```css
.play-button svg {
  transform: translateX(2px); /* physical on purpose: playback icons never mirror */
}
```

### Asymmetric glyphs

Stars, arrows and carets carry uneven weight. Fix the `viewBox` or path in the SVG so
no component needs an offset. Where the asset cannot change, nudge a wrapper
(`translate-x-px`), never the layout around it.

## Shadow rings

A ring replaces a depth-only border. Light mode uses three layers: a `1px` ring, a
tight lift and ambient depth.

```css
:root {
  --shadow-border:
    0 0 0 1px oklch(0 0 0 / 0.06),
    0 1px 2px -1px oklch(0 0 0 / 0.06),
    0 2px 4px 0 oklch(0 0 0 / 0.04);
  --shadow-border-hover:
    0 0 0 1px oklch(0 0 0 / 0.08),
    0 1px 2px -1px oklch(0 0 0 / 0.08),
    0 2px 4px 0 oklch(0 0 0 / 0.06);
}
/* Under the project's dark selector: depth layers vanish, keep one light ring */
--shadow-border: 0 0 0 1px oklch(1 0 0 / 0.08);
--shadow-border-hover: 0 0 0 1px oklch(1 0 0 / 0.13);

.card {
  border: 1px solid transparent; /* forced colors paints this when shadows drop */
  box-shadow: var(--shadow-border);
}
.card:hover {
  box-shadow: var(--shadow-border-hover); /* a short box-shadow transition is fine here */
}
```

The transparent border also keeps the box the size of the border it replaces. Gate
hover styling and choose its timing per [better-animation](../better-animation.md).

| Use a shadow ring | Keep a border |
| --- | --- |
| Cards and containers that need depth | Dividers between list items |
| Bordered button styles | Table cell boundaries |
| Dropdowns, popovers, modals | Form input outlines (measured 3:1 contrast) |
| Elements over images or mixed backgrounds | Hairline separators in dense UI |

## Elevation scale

Size tracks height: a higher surface gets more offset, more blur and slightly more
alpha. Each level layers tight, medium and wide shadows, and every `y` offset is
positive so one light source sits above.

```css
:root {
  --shadow-1: 0 1px 2px oklch(0 0 0 / 0.05);                     /* cards */
  --shadow-2: 0 1px 2px oklch(0 0 0 / 0.06),
              0 4px 8px oklch(0 0 0 / 0.04);                     /* dropdowns */
  --shadow-3: 0 1px 2px oklch(0 0 0 / 0.06),
              0 4px 8px oklch(0 0 0 / 0.04),
              0 12px 24px oklch(0 0 0 / 0.03);                   /* modals */
}
```

- Mixed offsets such as `4px 0` on one surface and `0 -4px` on another imply two light
  sources and read as broken.
- One heavy layer (`0 4px 16px / 0.2`) looks flat and dirty. Spread the same depth over
  layers of falling opacity.
- On a tinted background, tint the shadow toward that hue (`oklch(0.25 0.03 <hue> / 0.08)`)
  rather than raising black alpha, which turns the edge muddy.
- Dark surfaces barely show shadows. Raise elevated surfaces one lighter neutral step and
  add the light ring above; keep a dark shadow only for separation from the page.
- To animate elevation, fade a pseudo-element carrying the target shadow
  ([pseudo-elements.md](pseudo-elements.md#elevation-on-hover)).

## Alpha borders

A fixed gray only works on the background it was picked on. A translucent neutral
blends into whatever sits underneath, so one token serves cards, tinted sections and
images.

```css
:root {
  --border-subtle: oklch(0 0 0 / 0.08);
}
/* Under the project's dark selector */
--border-subtle: oklch(1 0 0 / 0.1);

.card { border: 1px solid var(--border-subtle); }
```

Alpha colors cannot be contrast-checked statically. Where the boundary must meet `3:1`
(inputs, toggles), measure the composited color on each real background, or use an
opaque token there; see [better-colors](../better-colors.md).

## Image outlines

```css
:root {
  --image-outline: oklch(0 0 0 / 0.1);
}
/* Under the project's dark selector */
--image-outline: oklch(1 0 0 / 0.1);

.content-image {
  outline: 1px solid var(--image-outline);
  outline-offset: -1px;
}
```

```tsx
<img
  className="outline outline-1 -outline-offset-1 outline-black/10 dark:outline-white/10"
  src={src}
  alt={alt}
/>
```

Never `outline-slate-*`, `outline-zinc-*`, a palette near-black such as `#111827` or the
accent: the tint shows as a colored fringe on the photo's edge. Skip transparent artwork
and deliberately borderless full-bleed photography. `outline` never changes the image's
size, and at `-1px` offset it sits inside the edge and follows the corner radius.

## Tactile button anatomy

For styles with depth, build a raised button from layers. Each is nearly invisible
alone; if the gradient is noticeable, it is too strong.

```css
.button-primary {
  background: linear-gradient(
    to bottom,
    color-mix(in oklch, var(--button-bg), white 4%),
    var(--button-bg)
  );
  color: var(--button-fg);
  box-shadow:
    0 0 0 0.5px oklch(0 0 0 / 0.3),          /* cut into the surface */
    inset 0 0 0 1px oklch(1 0 0 / 0.04),     /* ambient inner highlight */
    inset 0 1px 0 oklch(1 0 0 / 0.07),       /* top light */
    0 1px 2px oklch(0 0 0 / 0.1),            /* depth, tight to wide */
    0 2px 4px oklch(0 0 0 / 0.06),
    0 4px 8px oklch(0 0 0 / 0.03);
  text-shadow: 0 1px 1px oklch(0 0 0 / 0.15); /* lifts the label off the fill */
}
```

The anatomy targets dark, filled buttons. On a light fill, drop the text shadow and
lower the inner highlights; secondary buttons usually need only the shadow ring. Check
that the label still meets text contrast against the lightest part of the gradient.
