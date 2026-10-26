# Spacing and sizing

Units, a starting type scale, fluid display sizes, the heading mapping, tracking,
line-height for wrapping text and text trimming.

## Units

| Unit | Behavior |
| --- | --- |
| `px` | Fixed |
| `em` | Scales with the current font size |
| `rem` | Scales with the root font size |
| `%` on `font-size` | Relative to the parent's font size, behaves like `em` |
| `vw` | Scales with the viewport and ignores the reader's font size, so never alone on `font-size` |

## Type scale

Use the project's scale, or a framework's ready-made one that pairs each size with a
line height, or define one.

A starting point for a product interface, named by role:

```css
:root {
  --text-caption: 0.8125rem;
  --text-body: 1rem;
  --text-heading: 1.125rem;
  --text-title: 1.5rem;
  --text-display: 2.25rem;
}
```

| Role | Size | Line-height | Weight |
| --- | --- | --- | --- |
| Display | `2.25rem` (36px) | `1.1` | `600` |
| Title | `1.5rem` (24px) | `1.2` | `600` |
| Heading | `1.125rem` (18px) | `1.3` | `600` |
| Body | `1rem` (16px) | `1.5` | `400` |
| Caption | `0.8125rem` (13px) | `1.4` | `400` |

Emphasis within a role is one weight step up (`400` → `500`), not a size change.

## Fluid display sizes

Display and hero text can grow with the viewport. Clamp it, and keep a `rem` term in
the preferred value so browser zoom and the reader's font size still scale it:

```css
.display {
  font-size: clamp(2rem, 1.25rem + 3vw, 3.5rem);
}
```

Body, UI and input text stay on fixed scale steps. Check that the clamped maximum
stays within the scale's next step, and that text still doubles at 200% zoom.

## Heading hierarchy

```css
h1 { font-size: var(--text-display); }
h2 { font-size: var(--text-title); }
h3 { font-size: var(--text-heading); }
```

In a utility-class codebase, centralize the per-level classes in base styles or a
heading component rather than repeating them inline.

## Tracking by size

| Text | `letter-spacing` |
| --- | --- |
| Headings `24px` and up | `-0.01em` to `-0.02em`, tighter as size grows |
| Body copy | `0` |
| Uppercase labels and small caps at `14px` or less | `0.05em`, up to `0.1em` at the smallest sizes |

Values in `em`, so tracking follows the size it sits on.

## Line-height for wrapping text

```css
/* Bad: card description at heading leading */
.card-description { line-height: 1.1; }

/* Good: it wraps to 3 lines, so it reads as body text */
.card-description { line-height: 1.4; }
```

## Text trimming with text-box

`text-box` takes which edges to trim (`trim-both`, `trim-start`, `trim-end`) and where:

| Keyword | Trims at |
| --- | --- |
| `cap` | The cap height (top) |
| `alphabetic` | The baseline (bottom) |
| `text` | The font's own text edge, keeping room for descenders |

```css
/* trim top and bottom */
.badge {
  text-box: trim-both cap alphabetic;
}

/* trim only the top */
.heading {
  text-box: trim-start cap;
}

/* trim only the bottom */
.label {
  text-box: trim-end alphabetic;
}
```

Support is recent and not yet universal; check current support. Unsupported browsers
keep the default leading, so the trim is a progressive enhancement.
