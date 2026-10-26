# Values and color

Recipes for fluid tokens, units, color functions and typed custom properties. The
type scale
and fluid display sizes are [better-typography](../better-typography.md)'s; palettes,
ramps and contrast are [better-colors](../better-colors.md)'s.

## Make fluid values tokens

```css
:root {
  /* 16px at a 320px viewport, 22px at 1280px */
  --text-lede: clamp(1rem, 0.875rem + 0.625vw, 1.375rem);

  /* 48px at 320px, 128px at 1280px */
  --space-section: clamp(3rem, 1.333rem + 8.333vw, 8rem);
}

.lede { font-size: var(--text-lede); }
section { padding-block: var(--space-section); }
```

Work the preferred value out from two points, in px:

| Step | Formula | `--text-lede` |
| --- | --- | --- |
| Slope | `(max − min) / (wide − narrow)` | `(22 − 16) / (1280 − 320) = 0.00625` |
| `vw` part | `slope × 100` | `0.625vw` |
| `rem` part | `(min − slope × narrow) / 16` | `(16 − 2) / 16 = 0.875rem` |

- Never use a bare `vw` as the preferred value. It ignores the reader's font size and
  sits at the lower bound until the viewport is wide.
- Write the bounds in `rem` so they track the reader's font setting.
- The `vw` part does not grow with zoom. For font sizes, keep the maximum at or below
  about 2.5× the minimum, or at some widths 200% zoom fails to double it
  (WCAG 1.4.4).
- For a scale of more than two or three sizes, derive every step from one base and
  ratio in tokens; see
  [better-typography](../better-typography/spacing-and-sizing.md#type-scale).
  Replace `vw` with `cqi` for values that follow the container.

## Pick units by what they should follow

| Should follow | Unit |
| --- | --- |
| The reader's font size: type, spacing, query bounds | `rem` |
| The element's own font: icon gaps, padding inside labels | `em`, `cap`, `lh` |
| Line length | `ch` |
| The container | `%`, `fr`, `cqi` |
| The viewport | `svh`, `dvh`, `vw`; see [better-pwa](../better-pwa.md) |
| Nothing: hairlines, borders, outlines | `px` |

## Derive colors instead of repeating them

```css
:root {
  color-scheme: light dark;

  --color-bg: light-dark(oklch(99% 0 none), oklch(17% 0 none));
  --color-text: light-dark(oklch(20% 0 none), oklch(95% 0 none));
  --color-accent: light-dark(oklch(52% 0.17 255), oklch(72% 0.14 255));

  --color-accent-hover: color-mix(in oklch, var(--color-accent), var(--color-text) 12%);
  --color-accent-wash: color-mix(in oklch, var(--color-accent) 14%, transparent);
}

[data-theme="light"] { color-scheme: light; }
[data-theme="dark"] { color-scheme: dark; }
```

- With `light-dark()`, switch themes by setting `color-scheme`; a class toggle must
  set it too. Whether to use `light-dark()`, a class or the media query is
  [palette-generation.md](../better-colors/palette-generation.md#choosing-the-switching-mechanism)'s
  call. A `color-scheme` meta tag with `light dark` lets the first paint use the
  right canvas before CSS arrives.
- `light-dark()` accepts colors only. Images, shadows and other theme differences
  need their own rule.
- Neutrals carry the hue `none`
  ([color-formats.md](../better-colors/color-formats.md#converting)), so mixing with
  them stays neutral.
- Mix in `oklch` or `oklab`; `srgb` mixes go muddy through the middle. Gradient
  interpolation is [better-colors](../better-colors.md)'s.
- Relative color, `oklch(from var(--color-accent) calc(l - 0.08) c h)`, adjusts one
  channel. Use it for a single derived step, not chains of derivations nobody can
  read.
- Derived colors resolve at render time; measure the rendered pair before reporting
  contrast.

Support: `oklch()` and `color-mix()` are Baseline 2023, `light-dark()` 2024 (Chrome
123, Firefox 120, Safari 17.5), relative color 2024.

## Register custom properties you animate

```css
@property --reveal {
  syntax: "<number>";
  inherits: true;
  initial-value: 0;
}

.tile { --reveal: 0; }
.tile:focus-within { --reveal: 1; }

.tile-arrow {
  display: inline-block; /* individual transforms need a box */
  translate: calc(var(--reveal) * 0.25rem) 0;
}
.tile-more { opacity: var(--reveal); }

@media (prefers-reduced-motion: no-preference) {
  .tile { transition: --reveal 200ms var(--ease-out, ease-out); }
}
```

- Every syntax except `"*"` needs an `initial-value`, and it must be computationally
  independent: `1rem` is not allowed, `16px` is.
- Use `inherits: false` for values local to one element; it also makes style
  recalculation cheaper on large trees.
- A registered property with an invalid value falls back to its `initial-value`
  instead of `unset`, which makes failures easier to see.
- A script writes only the number, `el.style.setProperty("--reveal", "1")`; the
  easing and duration stay in CSS. Durations and curves are
  [better-animation](../better-animation.md)'s.
- Useful types beyond `<number>`: `<length-percentage>` for gradient stops, `<angle>`
  for conic gradients, `<color>` for animated color tokens.

Support: Chrome 85, Firefox 128, Safari 16.4.
