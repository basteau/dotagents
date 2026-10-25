# Palette generation

Producing the values once the structure is decided. For which ramps to build and what each step is for, see [palette-structure.md](palette-structure.md). Lightness here always means OKLCH `L`, from `0` to `1`.

## Start from the brand color

A brand color arrives as one value, usually a hex. Two decisions come before any ramp exists:

**Which step does it occupy?** A brand color meant for buttons and links belongs on the solid-fill step, `600` in Tailwind and `9` in Radix. Then the primary button renders the actual brand color, not an approximation.

**Is it pinned or snapped?** Pin a contractually fixed brand color. It stays exact, the ramp builds outward from it and that one step spaces slightly unevenly. Otherwise snap it onto the ramp so the spacing stays regular, which almost always looks better.

A brand color that fails contrast behind white text is still the brand color, just not the solid-fill step. Put it where its lightness lands and use a darker step for interactive fills. Never quietly darken the brand. `#3b82f6` lands on `500` and fills use `600`, because white on `#3b82f6` measures 3.68:1.

## What a correct ramp looks like

Properties of the finished ramp, checkable against any output in any notation:

- **Lightness changes steadily in OKLCH `L`.** HSL lightness is not perceptual, and evenly spaced HSL values bunch at one end.
- **Hue is constant end to end.** Every step is recognisably the same color. A wandering hue reads as two colors blended and will not sit correctly against a neutral built on a different hue.
- **Vividness peaks in the middle and falls off at both ends.** The lightest and darkest steps are nearly neutral; the middle carries the color. Holding full vividness into the extremes gives a `50` that glows and a `950` like ink spilled on the brand.
- **Steps are finer at the light end.** Light backgrounds need finer distinctions than dark ones. Keep `50`–`200` about `0.04`–`0.05` apart and mid-ramp steps about `0.07`–`0.10`. Even spacing across the whole range leaves too few pale steps, so `100` is already too dark for a subtle surface.
- **No two adjacent steps are indistinguishable.** Adjacent steps less than `0.03` apart mean the ramp has more steps than decisions. Drop one.
- **Both ends stop short of pure black and white**, which cannot carry hue. Keep `L` inside about `0.05`–`0.97`.
- **Every step is inside the target gamut.** An out-of-gamut step clips and collapses into its neighbour ([color-formats.md](color-formats.md#gamut)).

## Build it in OKLCH

The properties above map onto the three channels directly, which is why OKLCH is the space to do the math in whatever notation the project ships:

1. **Choose `L` per step** to the spacing above, finer at the light end.
2. **Hold `H`** at the brand color's hue.
3. **Set `C` as a share of that step's maximum**, the most chroma sRGB allows at that `L` and `H`. The gamut narrows toward black and white, so even a constant share makes absolute chroma peak mid-ramp. Taper the share toward the dark end so the darks do not read as ink.

Never compute these by hand or by eye. `culori`, `colorjs.io` and `chroma.js` all convert between notations, find the gamut boundary and interpolate perceptually:

```js
import { clampChroma, formatHex } from 'culori'

const h = 259 // the brand color's OKLCH hue
const steps = {
  // step: [OKLCH L, share of the in-gamut maximum chroma]
  50: [0.97, 0.95], 100: [0.93, 0.95], 200: [0.88, 0.95], 300: [0.81, 0.95],
  400: [0.71, 0.95], 500: [0.62, 0.95], 600: [0.55, 0.95], 700: [0.49, 0.9],
  800: [0.42, 0.8], 900: [0.38, 0.7], 950: [0.28, 0.6],
}

const maxChroma = (l) => clampChroma({ mode: 'oklch', l, c: 0.4, h }, 'oklch').c

const ramp = Object.fromEntries(
  Object.entries(steps).map(([step, [l, share]]) => [
    step,
    formatHex({ mode: 'oklch', l, c: share * maxChroma(l), h }),
  ]),
)
```

The numbers are a starting point. Check the output against the properties above, measure white text on the solid-fill step (`4.97:1` on this `600`), and adjust. The output format is the project's choice. Emit hex for a hex codebase, `oklch()` for a new system.

Interpolating between a light, a brand and a dark stop also works (`interpolate([...], 'oklab')` then `samples(11)`), but linear sampling spaces lightness evenly within each segment, so tighten `50`–`200` by hand. For a ramp the interpolation space is not a style choice, because sRGB interpolation produces muddy mid-steps. Decorative gradients are the opposite case ([color-usage.md](color-usage.md#gradients)).

## A reference ramp

Tailwind's blue in OKLCH shows every property, including the finer light end a linear generator lacks:

| Step | Hex | `L` | `C` | `H` | Share of max `C` |
| --- | --- | --- | --- | --- | --- |
| `50` | `#eff6ff` | 0.970 | 0.014 | 255 | ~100% |
| `100` | `#dbeafe` | 0.932 | 0.032 | 256 | 96% |
| `200` | `#bfdbfe` | 0.882 | 0.057 | 254 | 97% |
| `300` | `#93c5fd` | 0.809 | 0.096 | 252 | 97% |
| `400` | `#60a5fa` | 0.714 | 0.143 | 255 | 94% |
| `500` | `#3b82f6` | 0.623 | 0.188 | 260 | 92% |
| `600` | `#2563eb` | 0.546 | 0.215 | 263 | 86% |
| `700` | `#1d4ed8` | 0.488 | 0.217 | 264 | 75% |
| `800` | `#1e40af` | 0.424 | 0.181 | 266 | 62% |
| `900` | `#1e3a8a` | 0.379 | 0.138 | 266 | 53% |
| `950` | `#172554` | 0.282 | 0.087 | 268 | 46% |

Its hue moves about `13°` toward violet in the darks, on purpose: sRGB allows far more chroma near the blue primary at `H` `264`. A hue shift like that is a design decision. Drift is the same movement by accident.

**Detecting drift.** Convert every step to OKLCH and compare `H` across the steps that carry visible chroma. A spread above about `10°` is visible. HSL ramps are the usual source: `hsl(240 80% 20%)` sits at `H` `269` and `hsl(240 80% 90%)` at `H` `285`, a `16°` shift toward purple from a "constant" hue.

## Several hues at once

With an accent plus status ramps, the ramps must agree step for step. `red-500` and `blue-500` should read as equally bright and vivid, or a red button looks heavier than a blue one at the same step.

- **Match `L` exactly.** Same step, same brightness, across every hue.
- **Match the chroma share, not the chroma.** Hues do not share a maximum vividness, and no format makes them so. Give every ramp the same share of its own hue's maximum:

```css
:root {
  /* Same L, 80% of each hue's sRGB maximum, so three different C values */
  --blue-500: oklch(0.623 0.141 250);  /* max 0.176 */
  --green-500: oklch(0.623 0.157 145); /* max 0.196 */
  --red-500: oklch(0.623 0.202 25);    /* max 0.253 */
}
```

Yellows and cyans are the usual casualties, peaking much lower than reds and blues. Copy one chroma across and the warning color looks weak beside the danger one, or overshoots the gamut and clips.

## Dark mode

A dark palette is not the light one reversed. Reversal is the starting point, not the output.

Swap the semantic roles first, then tune the values:

```css
:root {
  --color-bg-page: var(--neutral-50);
  --color-text-primary: var(--neutral-950);
}

.dark {
  --color-bg-page: var(--neutral-950);
  --color-text-primary: var(--neutral-50);
}
```

Three things almost always need hand-tuning after the swap:

- **Vividness comes down.** A color that reads as confident on white reads as neon on near-black. Dark appearances need the accent a step or two lower in chroma share.
- **The dark end needs more separation.** Steps distinguishable as pale backgrounds collapse into each other as dark surfaces.
- **Contrast does not survive the mirror.** APCA scores mirrored pairs differently, and the reversed steps were never spaced for the new pairs, so remeasure every pair in both appearances ([contrast.md](contrast.md)).

### Choosing the switching mechanism

Use one mechanism throughout:

- **`prefers-color-scheme` alone** is correct with no theme toggle. Nothing to persist, nothing to hydrate.
- **A `.dark` class** is required as soon as users can override the system setting. The media query then sets only the initial value.
- **`light-dark()`** collapses both values into one declaration. It needs `color-scheme: light dark` and reads that property rather than a class, so a class-based toggle must set `color-scheme` too.

```css
:root {
  color-scheme: light dark;
  --color-bg-page: light-dark(#ffffff, #172554);
}
```

A media query setting some tokens and a class setting others gives a half-themed interface the moment a user overrides their system preference.
