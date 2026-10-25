# Cascade and tokens

Recipes for layer order, specificity, nesting, component scope, custom-property tokens and the reset.

## Declare the layer order once

```css
/* First line of the entry stylesheet: this list is the precedence, lowest first */
@layer reset, vendor, base, layouts, components, utilities;

@import url("datepicker.css") layer(vendor);

@layer base {
  a { color: var(--color-link); }
}

@layer components {
  .button { padding-block: var(--space-2); }
}
```

- The first mention of a layer name fixes its position. A file that says `@layer components { … }` before the order statement has already set the order, so keep the statement first and in one place.
- Unlayered rules beat every layer, whatever their specificity. Leave a rule unlayered only on purpose, such as a temporary override during migration.
- `!important` reverses the order: an important declaration in `reset` beats one in `utilities`. Another reason to keep it out of components.
- Nested layers such as `components.forms` sort inside their parent. Use them for large component libraries, not for every file.
- `@import` must come before all other rules except `@charset` and `@layer` statements.

Support: Chrome 99, Firefox 97, Safari 15.4.

## Keep specificity flat

```css
/* Default weight: one class */
.card { padding: var(--card-pad); }
.card-title { font-weight: 600; }

/* Zero-weight defaults any single class can override */
:where(.prose) :where(h2, h3) { margin-block-start: 2em; }

/* Bad: weight raised to win a fight */
#sidebar div.card .title { … }
.card.card { … }
```

- `:is()` and nesting take the weight of their heaviest argument, so one ID in the list makes every match ID-weight. `:where()` always weighs zero.
- State pseudo-classes such as `:hover` add class weight. That is expected, and a reason to keep the base selector to one class.
- When two rules fight, move one into a different layer before raising either selector.

## Nest for context, not structure

```css
.tab {
  color: var(--color-text-muted);

  &[aria-selected="true"] { color: var(--color-text); }
  &:focus-visible { outline-offset: 2px; }
  &::after { content: ""; }

  @container (width > 40rem) { padding-inline: var(--space-4); }
}
```

- Nest what describes this element: its states, attributes, pseudo-elements and the queries that change it. Do not mirror the DOM tree.
- `&` stands for the whole parent selector, like `:is(parent)`. `.a, #b { & .c {} }` gives `.c` ID weight.
- There is no concatenation. `&__title` and `&--large` are preprocessor features; in native CSS they are invalid or mean something else. Write `.card-title` as its own rule.
- A nested type selector works without `&` in current engines; write `& p` when the project still targets browsers from before relaxed nesting.

Support: Chrome 120, Firefox 117, Safari 17.2 for nesting without a leading `&`.

## Scope components by their root

```css
/* The component's API: custom properties with token defaults */
.card {
  --card-pad: var(--space-4);
  --card-surface: var(--color-surface);

  padding: var(--card-pad);
  background: var(--card-surface);
}

/* A parent tunes the card without touching its internals */
.sidebar .card { --card-pad: var(--space-3); }

/* Styles stop at the slot, so a nested component is left alone */
@scope (.card) to (.card-slot) {
  img { border-radius: inherit; }
  p { margin-block: 0; }
}
```

- One root class per component, parts as `.component-part` or the project's own naming scheme. The naming scheme matters less than using one consistently.
- `@scope` matches by proximity: when two scopes match, the nearer root wins at equal specificity. The root adds no weight to the rules inside; `:scope` adds a class's weight and `&` the root selector's.
- Without `@scope` support, the same intent is a child combinator from the root class, such as `.card > p`.

Support: `@scope` in Chrome 118, Safari 17.4 and Firefox 146.

## Make tokens custom properties

```css
:root {
  /* Primitives: raw values, named by what they are */
  --blue-600: oklch(52% 0.17 255);
  --size-4: 1rem;

  /* Semantic roles: what components read */
  --color-accent: var(--blue-600);
  --space-4: var(--size-4);
}

.badge {
  /* Fallback only where the token may be absent, e.g. a shared package */
  background: var(--badge-bg, var(--color-accent));
}
```

- Components read semantic tokens, never primitives, so a theme swaps one layer of names. Tier and naming rules: [token-naming.md](../better-colors/token-naming.md).
- A `var()` is substituted at computed-value time. If the result is invalid for the property, the declaration does not fall back to an earlier one; the property becomes `unset`, which inherits or resets. Test tokens in the property that uses them.
- A custom property inherits by default. Set component values on the component root, not on `:root`, so siblings stay independent.
- Keep math in tokens, such as `--gutter-wide: calc(var(--space-4) * 2)`, so components never repeat it.
- An empty value (`--x: ;`) is valid and useful as an off switch; `--x: initial` makes it guaranteed-invalid and triggers the fallback.

## Start from a small reset

```css
@layer reset {
  *, *::before, *::after { box-sizing: border-box; }

  body {
    margin: 0;
    min-block-size: 100svh;
  }

  img, svg, video, canvas {
    display: block;
    max-inline-size: 100%;
    block-size: auto;
  }

  input, button, textarea, select { font: inherit; }

  :where(h1, h2, h3, h4) { text-wrap: balance; }
}
```

- Text rendering, font smoothing, input sizes and wrapping defaults are [better-typography](../better-typography.md)'s; tap highlight and `touch-action` are [better-pwa](../better-pwa.md)'s. Add them from there, not from memory.
- `min-width: 0` on every element solves flex overflow but also lets sized icons and avatars collapse; if the project adds it, give fixed-size media `flex: none`.
- When retrofitting, introduce rules singly and compare the rendered page after each; a reset changes what already renders.

Leave these out:

- `text-box` trimming on `*`. It shrinks every text block and every button; trim only single-line labels, as [better-typography](../better-typography.md) describes.
- Resets that put every element in one grid cell or set `display: contents` on containers. They remove the boxes that layouts and components style.
- `text-rendering: optimizeLegibility` on body text. It costs rendering time for long text and `auto` is the recommended default.
- A global rule that forces every duration to `0.01ms` for reduced motion. Build the static state first and add motion inside the query; see [better-accessibility](../better-accessibility.md).
