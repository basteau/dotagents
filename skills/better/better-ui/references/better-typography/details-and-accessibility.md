# Details and accessibility

Underlines, selection, form text and decorative text.

## Underlines

From the font's own metrics:

```css
a {
  text-underline-position: from-font;
  text-decoration-thickness: from-font;
}
```

A dotted underline on an abbreviation:

```css
abbr {
  text-decoration: underline dotted;
}
```

Or tune manually. Keep the offset in `em` when the underline spans several sizes, and
leave `text-decoration-skip-ink` at its default `auto`:

```css
a {
  text-decoration-thickness: 1px;
  text-underline-offset: 0.2em;
  text-decoration-color: var(--color-gray-1000);
  transition: text-decoration-color 200ms ease-out;
}

a:hover {
  text-decoration-color: var(--color-gray-1200);
}
```

## Selection

- `::target-text` styles the phrase a shared link scrolls to.
- The Custom Highlight API styles ranges you pick yourself, like search matches,
  without extra markup.

## Forms and editable text

- `::placeholder` styles the hint in an empty field.
- `caret-color` colors the blinking insertion bar. A fully custom caret is hard to
  build and rarely worth it.

### iOS input zoom

**Size up on mobile.** The input renders at `16px` on small screens and drops to the
design size above the mobile breakpoint. Nothing to compensate, but the mobile input
no longer matches the desktop one.

```css
.input { font-size: 1rem; }
@media (min-width: 40rem) { .input { font-size: 0.875rem; } }
```

**Scale the text down.** Keep `font-size` at `16px` so Safari never zooms, then
render at the intended size with a transform. Widen the element by the inverse of the
scale so it still fills its container once shrunk. Divide `line-height` by the same
factor so the intended leading survives. Pin the transform origin to the start edge,
which is the right under RTL. Above the breakpoint, drop the transform and set the
real size.

```css
/* 13px rendered from a 16px font-size: 13 / 16 = 0.8125 */
.field {
  display: flex;
  align-items: center;
  height: 2.5rem;
  padding-inline: 0.625rem;
  overflow: hidden;
  border-radius: 10px;
  background: var(--color-bg-sunken);
}
.field:focus-within { outline: 2px solid var(--color-border-focus); outline-offset: 2px; }
.field input {
  flex-shrink: 0;
  width: calc(100% / 0.8125);
  height: 100%;
  font-size: 1rem;
  line-height: calc(1.125 / 0.8125);
  transform: scale(0.8125);
  transform-origin: left center;
  background: transparent;
  outline: none;
}
[dir="rtl"] .field input { transform-origin: right center; }
@media (min-width: 40rem) {
  .field input { width: 100%; font-size: 0.8125rem; line-height: 1.125; transform: none; }
}
```

`flex-shrink: 0` preserves the compensated width inside the flex wrapper. The wrapper
clips the oversized layout box and draws an unscaled focus outline. Use the project's
focus color and verify its contrast against the surrounding surface.

The transform shrinks the whole box, not only the glyphs, so let a wrapper draw the
field's surface and keep the input transparent. A background, border or ring on the
scaled element shrinks with the text and misses the intended hit area.

## Decorative text

| Property | Effect |
| --- | --- |
| `::first-letter` | Drop cap, widely supported |
| `::first-line` | Styles only the first line |
| `initial-letter` | Sizes the drop cap; support is partial (some engines need the `-webkit-` prefix, some lack it), so check current support and keep `::first-letter` styling as the fallback |
| `background-clip: text` | Clips a background or gradient to the letter shapes |
| `-webkit-text-stroke` | Outlines the letters; works across modern browsers despite the prefix |
| `text-shadow` | Like `box-shadow` but follows the character shapes |

If `-webkit-text-stroke` draws lines inside the letters, the font has overlapping
contours. Variable fonts often keep them unmerged, so use the static cut for stroked
text.

Gradient and clipped text still needs a solid `color` fallback, and contrast measured
where the gradient sits closest to the background.
