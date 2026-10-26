# Better CSS

Good CSS is small, adapts on its own and stays easy to override. This reference
covers how styles are structured and computed. Prefer one declaration that adapts
over a ladder of breakpoints, and a platform feature over a script; JavaScript
improves a result that already works without it.

Write in the project's authoring system and keep its file structure, naming, layers
and tokens. Plain stylesheets, CSS modules, CSS-in-JS and utility classes all take
the same declarations; where the system has no shorthand for one, write it the long
way rather than swap the technique for a breakpoint. The recipes are starting points
for projects without a convention, not mandates. Check each feature against the
browsers the project supports, and say so when one is missing somewhere.

This reference owns mechanics, not design values. Spacing scales, shared edges and
where layouts break belong to [better-layout](better-layout.md); the type scale,
fluid display sizes, wrapping and truncation to
[better-typography](better-typography.md); palettes, token names and contrast to
[better-colors](better-colors.md); radii, shadows and surfaces to
[better-polish](better-polish.md); easing, duration and enter and exit to
[better-animation](better-animation.md); focus rings and reduced motion to
[better-accessibility](better-accessibility.md); hover gating, press feedback,
viewport units and safe areas to [better-pwa](better-pwa.md). Tailwind syntax and
configuration live in [tailwind](tailwind.md).

## Cascade, scope and tokens

Open [cascade-and-tokens.md](better-css/cascade-and-tokens.md) for the recipes and
their edge cases: layer ordering rules, nesting weight, `@scope` fallbacks, `var()`
failure and which reset rules to skip.

- **Declare the layer order once.** Open the entry stylesheet with one `@layer`
  statement, such as `@layer reset, base, layouts, components, utilities;`, and
  import third-party CSS into a low layer of its own. Unlayered styles beat every
  layer.
- **Keep specificity flat.** One class is the default weight. Resolve precedence with
  layers, not IDs, qualified selectors, long chains or `!important`; wrap overridable
  defaults in `:where()`, and keep `!important` for utilities.
- **Nest for context, not structure.** Nest states, pseudo-elements and queries,
  about three levels at most. Native nesting has no string concatenation, so
  `&__title` never means `.card__title`.
- **Scope components by their root.** One root class per component, styling only its
  own parts. Change another component through the custom properties it exposes, never
  its internals. Name things by what they are, not the page they sit on.
- **Make tokens custom properties.** Components read semantic roles; tiers and names
  are [better-colors](better-colors.md)'s. A component exposes its tunable values as
  custom properties defaulting to tokens, with a `var()` fallback wherever the
  variable may be missing.
- **Start from a small reset** in its own layer, limited to `box-sizing`, zeroed
  `body` margin, block-level responsive media and inherited fonts on form controls.
  When retrofitting, add the rules singly and compare the render after each.

## Layout mechanics

Open [layout-mechanics.md](better-css/layout-mechanics.md) for the grid, flow, stack,
container-query and overflow recipes and their failure modes.

- **Size intrinsically before adding breakpoints:** `repeat(auto-fit,
  minmax(min(100%, 16rem), 1fr))` for equal tiles, a wrapping flex pair for a
  sidebar, `clamp()` for values that grow. Add a query only where the structure
  itself must change.
- **Lay out pages with one named grid** with content, breakout and full-width columns
  instead of the `section > .container` wrapper pair. Keep the project's existing
  container if it works; offer the grid when widths start to vary.
- **Let the parent own space between children:** `gap` when you know the children, a
  `* + *` flow rule for markup you cannot edit. A reusable component carries no outer
  margin.
- **Overlap and align with grid.** Stack layers in one grid cell instead of
  positioning them absolutely, and use `subgrid` when parts of sibling cards must
  line up. Keep `position: absolute` for layers that must not affect size.
- **Query the container in reusable components**, with `cqi` for sizes that follow
  it; keep media queries for page-level structure.
- **Let children shrink, then clip.** The `min-width: 0` and `minmax(0, 1fr)` fixes
  are [better-layout](better-layout.md)'s. Cut overflow with `overflow: clip` unless
  the element must scroll, and center with `safe` alignment so overflowing content
  stays reachable.
- **Write logical properties** for spacing, borders, insets and alignment. Shorthands
  with four values, such as `margin: 0 1rem 0 2rem`, remain physical. The mapping and
  exceptions are [better-layout](better-layout.md)'s.

## Values, units and color

Open [values-and-color.md](better-css/values-and-color.md) for the fluid-value math,
the unit table, `color-mix()` and `light-dark()` recipes and `@property`
registration.

- **Make fluid values tokens:** `clamp(min, rem + vw, max)` with `rem` bounds and
  never a bare `vw` in the middle, the math kept in a token. The type scale and its
  zoom limit are [better-typography](better-typography.md)'s.
- **Pick units by what they should follow:** the reader's font size, the local font,
  the line, the container or the viewport. Fixed `px` is for hairlines and borders.
- **Derive colors instead of repeating them.** Derive hover, tint and transparent
  variants with `color-mix(in oklch, …)` inside a token instead of hand-picking a
  second hex. With `light-dark()`, one token holds both themes and `color-scheme`
  flips them. Notation, palettes, the theme-switching mechanism and contrast are
  [better-colors](better-colors.md)'s.
- **Register custom properties you animate** with `@property`, since an unregistered
  one is a string and cannot transition. A registration is global, so give each a
  specific name.

## States, native UI and stacking

Open [states-and-native-ui.md](better-css/states-and-native-ui.md) for state
selectors, native dialog, popover, disclosure and anchor recipes, stacking and media
boxes.

- **Express state in selectors** instead of classes a script toggles: `:has()`,
  `:user-invalid`, `[aria-expanded]`, `[aria-current]`. Styling the ARIA attribute
  keeps visual and announced state in sync. Focus styling is
  [better-accessibility](better-accessibility.md)'s; hover gating is
  [better-pwa](better-pwa.md)'s.
- **Style native elements before rebuilding them.** `<dialog>`, `[popover]`,
  `<details>`, anchor positioning and `field-sizing` replace modal scripts,
  positioning libraries, disclosure handlers and auto-grow scripts. Their transitions
  are [better-animation](better-animation.md)'s.
- **Contain stacking** with `isolation: isolate` per component, a short named scale
  for page layers, and the top layer for overlays instead of raising numbers.
- **Give unknown media a box** with `aspect-ratio`, one `auto` axis and `object-fit`,
  so it holds its shape before and after loading.

## Keep rendering cheap

Which properties to animate is [better-animation](better-animation.md)'s; transition
them by name, never `all`. Skip rendering long off-screen sections with
`content-visibility: auto` and a reserved size, contain independent widgets, and ship
fewer, flatter files without `@import` chains. Open
[rendering.md](better-css/rendering.md) when a measurement shows style, layout or
paint cost.

## Before you finish

| Detection pattern | Fix |
| --- | --- |
| `!important` outside utilities, or selectors raised to win (`#id`, `.a.a`, `body .x`) | A layer, or a lower-weight default wrapped in `:where()` |
| Layer order implied by import order, or third-party CSS unlayered | One `@layer` statement first; vendor CSS imported into a low layer |
| Nesting four or more levels deep, or `&__part` / `&-modifier` in native CSS | Flatten to one class per part; concatenation is a preprocessor feature |
| A component styling another component's internals | Expose a custom property on the inner component and set it |
| The same color or size literal repeated across rules | A token; derive variants with `color-mix()` or `calc()` |
| A hand-picked second hex or `rgba()` for a hover or tint | The role's ramp step, or `color-mix(in oklch, var(--base), …)` |
| Media-query ladder changing a column count | `auto-fit` or `auto-fill` with `minmax(min(100%, X), 1fr)` |
| Nested `max-width` wrappers or negative margins for full-bleed bands | A named content grid with breakout and full-width columns |
| Children carrying `margin-top` to space a stack | `gap` on the parent, or a `* + *` flow rule for uncontrolled content |
| `position: absolute` layers with offsets inside a box sized by them | Grid stack: `grid-area: 1 / 1` on every layer |
| `clamp()` with a bare `vw` preferred value or `px` bounds | `rem + vw` preferred value, `rem` bounds |
| `overflow: hidden` only to cut overflow | `overflow: clip`, unless it scrolls, resizes or needs an ellipsis fallback |
| A script toggling a parent class, a "touched" class or an open class | `:has()`, `:user-invalid`, `[aria-expanded]` |
| A positioning library or `getBoundingClientRect()` for a dropdown | `popover` plus anchor positioning, inside `@supports` |
| `z-index: 9999` | `isolation: isolate` on the component, a named layer scale, or the top layer |
| `transition: all` | Name each property |
| A transitioned custom property that jumps | Register it with `@property` |
| `@import` chains served unbundled | One bundled file, or `<link>` elements the browser fetches in parallel |

## Reporting

**Severity.** `HIGH` breaks rendering in a supported browser: a rule dropped because
one selector in its list is unknown there, a variable that resolves invalid and
unsets a property, a removed focus outline, or overflow that hides content or an
action. `MEDIUM` forces future overrides or drift: specificity or layer fights,
duplicated literals in place of tokens, breakpoint ladders where intrinsic sizing
works, scripts doing a native feature's job. `LOW` is consistency: physical
properties in a non-localized layout, naming, nesting depth.

**Verification.** Without a browser, run the project's CSS lint and build, check
every feature against its browser targets, and search for `!important`, IDs,
`transition: all` and repeated literals. With one, read the computed styles and the
cascade layers in developer tools, resize through every supported width, and load the
page in the oldest supported engine to see what each fallback renders. Report every
check you could not run as `Not verified`.

For reviews, use [the shared review format](better-ui-review.md#report).
