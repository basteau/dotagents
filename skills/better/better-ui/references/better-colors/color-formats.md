# Color formats

Which notation to write colors in, how to convert between them, what happens at the edges of a display's gamut and what needs a fallback. Every other rule in this reference is stated perceptually and holds whatever notation you write.

## Choosing a notation

| Notation | Good for | Weakness |
| --- | --- | --- |
| Hex | Universal support, compact, what design tools hand you | Opaque. No channel is readable or editable by hand |
| `rgb()` | Same reach as hex, readable alpha | Channels do not correspond to anything a designer thinks about |
| `hsl()` | Channels look like design controls | Its lightness is not perceptual and its hue drifts; a ramp built by varying lightness bunches at one end and shifts hue |
| `oklch()` | Perceptually uniform lightness, stable hue, predictable ramps | Not every value is displayable, so high chroma needs a gamut check |

**Match whatever the project already uses.** Notation is not a defect: a project on hex is not doing it wrong.

**For a genuinely new color system, `oklch()` is the best default.** Even lightness steps stay even, a fixed hue stays fixed, and a contrast fix touches one channel. See [palette-generation.md](palette-generation.md).

## Reading OKLCH

```css
oklch(L C H)          /* lightness, chroma, hue */
oklch(L C H / alpha)  /* alpha uses a slash, never a comma */
```

| Channel | Range | Meaning |
| --- | --- | --- |
| `L` | `0`–`1` | Perceived lightness. `0` is black, `1` white |
| `C` | `0` up | Colorfulness, `0` being gray. sRGB tops out near `0.32`, P3 near `0.36`, and the maximum depends on `L` and `H` |
| `H` | `0`–`360` | Hue angle in degrees. Meaningless when `C` is `0` |
| alpha | `0`–`1` | Optional. Omit it when it is `1` |

Two consequences matter in practice:

- **Equal `L` means equal brightness across hues.** HSL `50%` lightness is bright in yellow and dark in blue. OKLCH `0.62` is the same brightness in both.
- **Equal `C` is not equal vividness across hues.** Chroma is absolute, so match the *proportion* of each hue's maximum instead ([palette-generation.md](palette-generation.md)).

## Converting

Convert when the user asks, when an agreed migration is in scope, or when the project is standardizing on a notation and this value is the straggler. Never convert an isolated value in a project that deliberately uses something else and never because this reference happened to load.

When conversion is in scope, change the values and nothing else:

- Leave CSS keywords alone: `currentColor`, `inherit`, `transparent`, `initial`, `unset`.
- Leave gradient functions alone. Convert the color stops inside them; do not touch the interpolation method.
- Leave colors in third-party configs that expect a specific format.
- Preserve comments and formatting.

Inputs are 3-, 4-, 6- and 8-digit hex, `rgb()`/`rgba()` and `hsl()`/`hsla()`. Compute every value with a color library; never estimate one.

```css
/* Before */
color: #3b82f6;
background: #1e293b;
border: 1px solid rgba(0, 0, 0, 0.1);

/* After */
color: oklch(0.623 0.188 259.815);
background: oklch(0.279 0.037 260.031);
border: 1px solid oklch(0 0 0 / 0.1);
```

Write `L` and `C` to 3 decimals and `H` to at most 3, dropping trailing zeros and writing `-0` as `0`. Grays convert to a chroma of about `0` with an arbitrary hue (`#808080` reports `H` near `90`). Write `0` for both, as in `oklch(0.6 0 0)`, so the noise does not read as a tint.

Bulk conversion is a migration, not cleanup. It shifts every rendered color by a rounding margin and touches files nobody asked about, so it has to be the task rather than a side effect.

## Gamut

Every sRGB color exists in Display P3, but not the reverse. The extra room is only at high chroma and varies by hue. At OKLCH `L` `0.65`, green gains about `0.07` of chroma while blue gains about `0.015`.

The sRGB boundary is irregular. Maximum chroma depends on hue and lightness together:

| `L` | Highest maximum `C` | Lowest maximum `C` |
| --- | --- | --- |
| `0.5` | Purple, `H` near `285`: `0.29` | Cyan, `H` near `200`: `0.085` |
| `0.7` | Magenta, `H` near `330`: `0.31` | Cyan, `H` near `200`: `0.12` |
| `0.9` | Green, `H` near `130`: `0.24` | Blue, `H` near `270`: `0.05` |

The peak moves with lightness, so a ramp that copies one chroma across its steps fits the gamut at some steps and overshoots at others.

A color more vivid than its display can render gets gamut-mapped, which in current browsers mostly means clipped channel by channel. Clipping flattens neighbouring steps into one rendered color and can shift hue and lightness too, so the top of a ramp loses its distinctions on an sRGB screen.

The fix is to reduce chroma while holding `L` and `H`: `oklch(0.7 0.25 150)` is outside sRGB, `oklch(0.7 0.19 150)` is inside. culori's `clampChroma(color, 'oklch')` and colorjs.io's `toGamut()` find the boundary. Generate ramps against sRGB unless the product is display-restricted, and add P3 as an enhancement:

```css
.success {
  background: #11ad32; /* oklch(0.65 0.2 145), inside sRGB */
}

@media (color-gamut: p3) {
  .success {
    background: oklch(0.65 0.27 145);
  }
}
```

Order matters. The sRGB value comes first, so an sRGB screen shows a color you chose rather than one the browser clipped. The P3 rule overrides only where it will render.

## Browser support and fallbacks

| Feature | Baseline | An unsupporting browser |
| --- | --- | --- |
| `oklch()`, `oklab()`, `color-mix()` | 2023 | Drops the declaration |
| `@media (color-gamut: p3)` | 2023 | Skips the block, keeping the sRGB value |
| Gradient interpolation, as in `in oklab` | 2024 | Drops the whole gradient |
| Relative color syntax, `light-dark()` | 2024 | Drops the declaration |

Check the project's actual browser matrix before adding fallbacks. On a modern baseline they are dead weight. Where the matrix predates support, declare the old value first and layer the new one with `@supports`, nesting the P3 enhancement inside:

```css
.accent {
  background: #3b82f6;
}

@supports (color: oklch(0 0 0)) {
  .accent {
    background: oklch(0.623 0.188 259.815);
  }

  @media (color-gamut: p3) {
    .accent {
      background: oklch(0.623 0.21 259.815);
    }
  }
}
```

Inside one rule, a duplicate declaration also works (`background: #3b82f6; background: oklch(…);`), because a browser discards a value it cannot parse and keeps the previous one. Custom properties are the exception. A `--token` accepts any value at parse time, so an unsupported color fails only where `var()` uses it and the fallback is already gone. Guard token definitions with `@supports`.

## Modern CSS worth knowing

- **`color-mix()`** derives one color from another, as in `color-mix(in oklab, var(--color-accent-solid) 15%, white)` for a tinted background. Useful for states, but keep generated values out of the token layer, since a mixed color cannot be inspected in a design tool.
- **Relative color syntax** adjusts one channel of an existing color. `oklch(from var(--color-accent-solid) calc(l - 0.1) c h)` darkens the accent by hand. Powerful and easy to overuse, since a token defined by three chained derivations is unreadable.
- **`light-dark()`** puts both appearances in one declaration. See [palette-generation.md](palette-generation.md).

All three resolve at render time. Compute the resolved value with a color library, or measure the rendered result, before reporting contrast.
