# Better CSS

Good CSS is small, adapts on its own and stays easy to override. This reference covers how styles are structured and computed: the cascade, selectors, custom properties, layout mechanics, value and color functions, native component styling and rendering cost. Prefer one declaration that adapts over a ladder of breakpoints, and a platform feature over a script; JavaScript improves a result that already works without it.

Write in the project's authoring system and keep its file structure, naming, layers and tokens. Plain stylesheets, CSS modules, CSS-in-JS and utility classes all take the same declarations; where the system has no shorthand for one, write it the long way rather than swap the technique for a breakpoint. The recipes are starting points for projects without a convention, not mandates. Check each feature against the browsers the project supports, and say so when one is missing somewhere.

This reference owns mechanics, not design values. Spacing scales, shared edges and where layouts break belong to [better-layout](better-layout.md); the type scale, fluid display sizes, wrapping and truncation to [better-typography](better-typography.md); palettes, token names and contrast to [better-colors](better-colors.md); radii, shadows and surfaces to [better-polish](better-polish.md); easing, duration and enter and exit to [better-animation](better-animation.md); focus rings and reduced motion to [better-accessibility](better-accessibility.md); hover gating, press feedback, viewport units and safe areas to [better-pwa](better-pwa.md). Tailwind syntax and configuration live in [tailwind](tailwind.md).

## Declare the layer order once

Open the entry stylesheet with one `@layer` statement, such as `@layer reset, base, layouts, components, utilities;`, so precedence comes from the list rather than from source order or selector weight. Import third-party CSS into a low layer of its own. Unlayered styles beat every layer, so layer everything you own or know exactly what is left out. See [cascade-and-tokens.md](better-css/cascade-and-tokens.md#declare-the-layer-order-once).

## Keep specificity flat

One class is the default weight for every rule. IDs, qualified selectors such as `div.card`, long descendant chains and `!important` start specificity wars; resolve precedence with layers instead, and wrap defaults meant to be overridden in `:where()`. Keep `!important` for utilities whose only job is to win. See [cascade-and-tokens.md](better-css/cascade-and-tokens.md#keep-specificity-flat).

## Nest for context, not structure

Nest states, pseudo-elements and queries that belong to a rule, and stop at about three levels. A nested rule takes the specificity of its parent list as if wrapped in `:is()`, and native nesting has no string concatenation, so `&__title` never means `.card__title`. See [cascade-and-tokens.md](better-css/cascade-and-tokens.md#nest-for-context-not-structure).

## Scope components by their root

Give each component one root class and style only its own parts. Change another component through the custom properties it exposes, never by reaching into its internals. `@scope` with a lower boundary keeps rules out of nested slots where the project's browsers support it. Name things by what they are, not by the page they sit on. See [cascade-and-tokens.md](better-css/cascade-and-tokens.md#scope-components-by-their-root).

## Make tokens custom properties

Primitives feed semantic roles, and components read the roles. A component exposes its tunable values as its own custom properties, defaulting to tokens. A `var()` that resolves to an invalid value makes the property `unset`, not the previous declaration, so give a fallback wherever the variable may be missing. Token tiers and names are [better-colors](better-colors.md)'s. See [cascade-and-tokens.md](better-css/cascade-and-tokens.md#make-tokens-custom-properties).

## Start from a small reset

Keep the reset short, in its own layer, and limited to rules every element needs: `box-sizing`, zeroed `body` margin, block-level responsive media, inherited fonts on form controls. When retrofitting a project, introduce the rules singly and compare the render after each. Some popular reset rules do more harm than good; the reasons are in [cascade-and-tokens.md](better-css/cascade-and-tokens.md#start-from-a-small-reset).

## Size intrinsically before adding breakpoints

Let content and available space drive the layout: `repeat(auto-fit, minmax(min(100%, 16rem), 1fr))` for equal tiles, a wrapping flex pair for a sidebar, `clamp()` for values that grow. Add a query only where the structure itself must change. See [layout-mechanics.md](better-css/layout-mechanics.md#size-intrinsically-before-adding-breakpoints).

## Lay out pages with one named grid

A grid with named lines for content, breakout and full-width columns replaces the `section > .container` wrapper pair. Any child then picks its width with one `grid-column`, with no negative margins or extra wrappers. Keep the project's existing container if it works; offer the grid when widths start to vary. See [layout-mechanics.md](better-css/layout-mechanics.md#lay-out-pages-with-one-named-grid).

## Let the parent own space between children

Space siblings with `gap` on the parent when you know the children, and with a `* + *` flow rule for markup you cannot edit, such as CMS output. A reusable component carries no outer margin of its own, so it fits any context. See [layout-mechanics.md](better-css/layout-mechanics.md#let-the-parent-own-space-between-children).

## Overlap and align with grid

Stack layers in one grid cell instead of positioning them absolutely, so the container still sizes to its content. Use `subgrid` when parts of sibling cards, such as titles and footers, must line up across a row. Keep `position: absolute` for layers that must not affect size. See [layout-mechanics.md](better-css/layout-mechanics.md#overlap-and-align-with-grid).

## Query the container in reusable components

A component that appears in slots of different widths responds to its container, not the viewport. Make a stable ancestor the container, use `cqi` units for sizes that follow it, and keep media queries for page-level structure. Containment rules and failure modes are in [layout-mechanics.md](better-css/layout-mechanics.md#query-the-container-in-reusable-components).

## Let children shrink, then clip

Flex and grid items refuse to shrink below their content, so long text or a wide table overflows; [better-layout](better-layout.md) has the `min-width: 0` and `minmax(0, 1fr)` fixes. Cut overflow with `overflow: clip` unless the element must scroll, and center with `safe` alignment so content that outgrows its box stays reachable. See [layout-mechanics.md](better-css/layout-mechanics.md#let-children-shrink-then-clip).

## Write logical properties

Use `inline` and `block` properties for spacing, borders, insets and alignment, so layouts mirror and rotate with writing direction. Shorthands with four values, such as `margin: 0 1rem 0 2rem`, remain physical. The mapping and the exceptions are [better-layout](better-layout.md)'s.

## Make fluid values tokens

Write a growing value once as `clamp(min, rem + vw, max)`, with both bounds in `rem` and never a bare `vw` in the middle. Derive the slope from two points and keep the math in a token; components only read the token. Fluid font sizes have a zoom limit; [better-typography](better-typography.md) owns the scale. See [values-and-color.md](better-css/values-and-color.md#make-fluid-values-tokens).

## Pick units by what they should follow

Choose each unit for the thing it should track: the reader's font size, the local font, the line, the container or the viewport. Fixed `px` is for hairlines and borders.

| Should follow | Unit |
| --- | --- |
| The reader's font size: type, spacing, query bounds | `rem` |
| The element's own font: icon gaps, padding inside labels | `em`, `cap`, `lh` |
| Line length | `ch` |
| The container | `%`, `fr`, `cqi` |
| The viewport | `svh`, `dvh`, `vw`; see [better-pwa](better-pwa.md) |
| Nothing: hairlines, borders, outlines | `px` |

## Derive colors instead of repeating them

Write colors in `oklch()`, give neutrals a hue of `none`, and derive hover, tint and transparent variants with `color-mix(in oklch, …)` instead of hand-picking a second hex. Put both themes in one token with `light-dark()` and switch with `color-scheme`. Palette design and contrast checks are [better-colors](better-colors.md)'s. See [values-and-color.md](better-css/values-and-color.md#derive-colors-instead-of-repeating-them).

## Register custom properties you animate

An unregistered custom property is a string and cannot transition. Register it with `@property` to give it a type, an initial value and inheritance, and let one eased number drive several declarations. A registration is global, so give each one a specific name. See [values-and-color.md](better-css/values-and-color.md#register-custom-properties-you-animate).

## Express state in selectors

Read state from the DOM instead of classes a script toggles: `:has()` for parents and page state, `:user-invalid` for touched fields, `[aria-expanded]` and `[aria-current]` for widget state. Styling the ARIA attribute keeps the visual and announced states in sync. Focus styling is [better-accessibility](better-accessibility.md)'s; hover gating is [better-pwa](better-pwa.md)'s. See [states-and-native-ui.md](better-css/states-and-native-ui.md#express-state-in-selectors).

## Style native elements before rebuilding them

`<dialog>`, `[popover]`, `<details>`, anchor positioning and `field-sizing` replace modal scripts, positioning libraries, disclosure handlers and auto-grow scripts. They bring keyboard behavior and the top layer for free. Their enter and exit transitions are [better-animation](better-animation.md)'s. See [states-and-native-ui.md](better-css/states-and-native-ui.md#style-native-elements-before-rebuilding-them).

## Contain stacking

Give each component `isolation: isolate` so its internal `z-index` values cannot escape. Keep page-level layers on a short named scale, and put overlays in the top layer with `dialog` or `popover` instead of raising numbers. See [states-and-native-ui.md](better-css/states-and-native-ui.md#contain-stacking).

## Give unknown media a box

Images, video and embeds whose files you do not control get a box with `aspect-ratio`, one `auto` axis and `object-fit`, so they hold their shape before and after loading. See [states-and-native-ui.md](better-css/states-and-native-ui.md#give-unknown-media-a-box).

## Keep rendering cheap

Transition named properties, preferring `transform`, `opacity` and `filter`, never `all`. Skip rendering long off-screen sections with `content-visibility: auto` and a reserved size, and contain independent widgets. Ship fewer, flatter files without `@import` chains. Measure before tuning selectors; their matching cost rarely matters. See [rendering.md](better-css/rendering.md).

## Before you finish

| Detection pattern | Fix |
| --- | --- |
| `!important` outside utilities, or selectors raised to win (`#id`, `.a.a`, `body .x`) | A layer, or a lower-weight default wrapped in `:where()` |
| Layer order implied by import order, or third-party CSS unlayered | One `@layer` statement first; vendor CSS imported into a low layer |
| Nesting four or more levels deep, or `&__part` / `&-modifier` in native CSS | Flatten to one class per part; concatenation is a preprocessor feature |
| A component styling another component's internals | Expose a custom property on the inner component and set it |
| The same color or size literal repeated across rules | A token; derive variants with `color-mix()` or `calc()` |
| A second hex or `rgba()` for a hover or tint | `color-mix(in oklch, var(--base), …)` |
| A neutral written as `oklch(L 0 0)` and later mixed | Hue `none`; a written `0` is red and tints every mix |
| A `.dark` block redefining every color token | `light-dark()` in each token, switched by `color-scheme` |
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

**Severity.** `HIGH` breaks rendering in a supported browser: a rule dropped because one selector in its list is unknown there, a variable that resolves invalid and unsets a property, a removed focus outline, or overflow that hides content or an action. `MEDIUM` forces future overrides or drift: specificity or layer fights, duplicated literals in place of tokens, breakpoint ladders where intrinsic sizing works, scripts doing a native feature's job. `LOW` is consistency: physical properties in a non-localized layout, naming, nesting depth.

**Verification.** Without a browser, run the project's CSS lint and build, check every feature against its browser targets, and search for `!important`, IDs, `transition: all` and repeated literals. With one, read the computed styles and the cascade layers in developer tools, resize through every supported width, and load the page in the oldest supported engine to see what each fallback renders. Report every check you could not run as `Not verified`.

For reviews, use [the shared review format](better-ui-review.md#report), one row per root cause listing every location it appears in.
