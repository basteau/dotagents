# Colors

A color system is a small set of ramps, named by role and verified against the
backgrounds they actually render on. Most color bugs are system bugs. A value picked
in isolation, a token borrowed because it looked right, a pair nobody measured.

Never report a contrast value you did not measure, and never estimate a color you
could compute. Notation, a tinted neutral and a gradient's interpolation space are
project choices, not findings. A broken role mapping, a failing pair and an
out-of-gamut step are findings. Perceived lightness throughout means OKLCH `L`, from
`0` to `1`.

Which contrast requirement a pair must meet belongs to
[better-accessibility](better-accessibility.md); this reference measures the pair and
fixes it. Surfaces, shadows and icon color belong to
[better-polish](better-polish.md); `color-mix()`, `light-dark()` and custom-property
mechanics to [better-css](better-css.md); Tailwind `@theme` mapping to
[tailwind](tailwind.md).

## The system and its tokens

- **Match the project's color system.** Reuse the project's tokens and notation. A
  second representation added to fix one value makes the palette harder to reason
  about. A consistent hex system beats hex with `oklch()` scattered through it. For a
  new system, `oklch()` is the best default, because its channels behave the way the
  ramp rules below describe. Everywhere else, a color library produces the same ramp
  in the project's own notation ([color-formats.md](better-colors/color-formats.md)).
- **A system is ramps, not colors.** One neutral ramp, one accent ramp and only the
  status ramps the product actually renders. A `warning` ramp nothing imports is
  maintenance for zero pixels. A second accent hue earns its place only when two
  things must be distinguishable at a glance. Otherwise use more steps of the one
  accent ramp.
- **Every step has a job.** A ramp is not a gradient to pick from by eye. Each step
  exists because a role needs it: page background, component hover, border, solid
  fill, body text. Do not generate a step no role consumes. Both common numbering
  conventions, by lightness (`50`–`950`, as in Tailwind) and by role (`1`–`12`, as in
  Radix), map to those roles
  ([palette-structure.md](better-colors/palette-structure.md)).
- **Name primitives by hue, semantics by role.** Primitives name a value
  (`--blue-600`) and are never applied in a component. Semantic tokens name a job
  (`--color-text-secondary`), point at a primitive and are the only tier components
  reference. That seam is what lets a theme repoint colors without touching
  components ([token-naming.md](better-colors/token-naming.md)).
- **Use a token only in its role.** Never borrow a token because its value is right
  today. A separator used as a text color works until borders get lighter, and then
  the text goes with them. If a role has no token, add the token.

## Building ramps

- **Build ramps in OKLCH, whatever you ship.** Choose `L` per step, finer at the
  light end. Hold `H` at the brand hue. Set `C` as a share of the most chroma the
  gamut allows at that `L` and `H`, tapering toward the dark end. That gives the four
  properties of a good ramp: even perceived steps, one hue, vividness peaking
  mid-ramp and pale steps fine enough to tell apart. Both ends stop short of pure
  black and white. Use a color library, never your eye, and emit the project's
  notation ([palette-generation.md](better-colors/palette-generation.md)).
- **Match chroma share across hues.** Ramps that sit side by side share `L` at every
  step and the same *share* of each hue's own maximum chroma. One chroma number
  copied across hues leaves yellow and cyan weak and pushes others out of gamut
  ([palette-generation.md](better-colors/palette-generation.md#several-hues-at-once)).
- **Stay inside the gamut you ship to.** Maximum chroma depends on hue and lightness,
  so check every step. An out-of-gamut color is clipped, which collapses neighbouring
  steps and can shift hue. Reduce chroma at constant `L` and `H`. Declare the sRGB
  value first and add the vivid P3 one inside `@media (color-gamut: p3)`. Add
  `@supports` fallbacks only when the browser matrix needs them
  ([color-formats.md](better-colors/color-formats.md#gamut)).

## Using color

- **One color, one meaning.** Use a color for one purpose across the whole interface,
  treating anything within `15°` of OKLCH hue as the same color. If the accent means
  interactive, that hue on static text tells users to click something that is not
  clickable, and an interactive element rendered neutral misleads just as badly
  ([color-usage.md](better-colors/color-usage.md)). Never let color be the only
  carrier of meaning. Pair it with an icon or a label, a requirement
  [better-accessibility](better-accessibility.md) owns.
- **Fill exactly one action per view.** When filled color encodes primary emphasis,
  one primary action gets it and peers stay neutral. Put the color on the background,
  not the label. A filled button reads as primary across the room; accent-colored
  text on a neutral button reads as a link. Keep an established hierarchy that
  already signals emphasis another way. Several colored backgrounds are fine when
  they encode distinct states or categories rather than competing as peers.
- **Measure the rendered pair, then report.** Measure a foreground against the
  background it actually renders on, not the page background, in every appearance.
  Fix contrast by moving `L`, never hue. For a review, report the pair, measured
  value and missed threshold. For a build or requested improvement, correct failing
  pairs within scope while preserving the visual direction, then remeasure
  ([contrast.md](better-colors/contrast.md)).
- **Pick a gradient's interpolation space.** The space is a look, not a correctness
  setting. Default to `in oklab` for even brightness. Reach for `in oklch` when a
  two-hue gradient goes gray in the middle, accepting the hues it sweeps through. The
  sRGB default darkens and mutes the midpoint
  ([color-usage.md](better-colors/color-usage.md#gradients)).

## Before you finish

| Pattern | Fix |
| --- | --- |
| A hex, `rgb(` or `oklch(` literal in a component file where a token exists | Reuse or add the role token, in the project's notation |
| One `oklch(` value in a codebase otherwise written in hex | Write it in the established notation unless a migration is in scope |
| A primitive (`var(--blue-600)`, or a utility class built from one) in a component file | Point a semantic token at the primitive and use that |
| A semantic name containing a hue or a component, as in `--color-blue-button` or `--color-sidebar-gray` | Rename it for its role: `--color-accent-solid`, `--color-bg-surface` |
| `--color-primary` and `--color-text-primary` both defined | Rename the brand token `accent` |
| A `border` or `separator` token inside `color:`, or a `text` token inside `background:` | Add a token for the missing role |
| `hsl(` ramp values that differ only in lightness, or OKLCH `H` spreading more than `10°` across chromatic steps | Rebuild with a constant hue, in the project's notation |
| `50`–`200` stepping as far apart in `L` as mid-ramp steps do | Tighten the light end to about `0.04`–`0.05` per step |
| One chroma or saturation number copied across every hue's ramp | Use the same share of each hue's own maximum |
| A status hue within `15°` of the accent hue | Move it at least `15°` away, or give the destructive action a distinct treatment |
| Dark tokens equal to the light ramp in reverse step order | Lower the accent's chroma, widen the dark end and remeasure every pair |
| Color tokens set under both `prefers-color-scheme` and a `.dark` class | Pick one switching mechanism and use it throughout |
| A contrast fix that changes hue and leaves lightness | Change `L`, the channel contrast responds to |
| `prefers-contrast: more` overriding a color for both appearances in one block | Give each appearance its own increased-contrast value |
| Text on a color with alpha, such as `rgba(`, `color-mix(` with `transparent` or a utility's `/50` modifier | Measure the rendered result, or use a solid token |
| White text on a mid-ramp (`500`) fill | Measure it; white on most `500` hues fails 4.5:1, so move the fill a step darker |
| `oklch()` chroma beyond sRGB with no sRGB value declared before it | Declare the sRGB value first, then the vivid one inside `@media (color-gamut: p3)` |
| A gray converted to `oklch(L 0.0001 89.9)`, or written `oklch(L 0 0)` and then mixed | Write `oklch(L 0 none)` |
| An unsupported color function inside a custom property, on a matrix that needs fallbacks | Guard the token definition with `@supports` |

## Reporting

**Severity.** Beyond the contrast and color-alone [escalation
triggers](better-ui-review.md#rank-by-user-impact), a semantic color used against its
meaning is `HIGH` whatever the surface. A theme, token or gamut failure is usually
`MEDIUM`; isolated polish is `LOW`.

**Verification.** Without a browser: token values, the gamut of every declared color,
both theme blocks present and contrast computed from the declared token pair. With
one: the background actually rendered behind the text, including opacity and any
image beneath it, measured in both light and dark. Remeasure corrected pairs after
scoped fixes. Report every check you could not run as `Not verified`.

For reviews, use [the shared review format](better-ui-review.md#report).
