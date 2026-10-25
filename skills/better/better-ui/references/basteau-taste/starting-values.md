# Starting values

A starter for a new site with no design system. Replace every value with the
project's brand; keep the roles. Mechanics behind each choice live in the sibling
references ([typography](../better-typography.md), [colors](../better-colors.md),
[animation](../better-animation.md), [accessibility](../better-accessibility.md)).

## Tokens

| Role | Light | Dark | Notes |
| --- | --- | --- | --- |
| Ground | `#FFFFFF` or bone `#F3F0E8` | `#0B0B0C` | Warm brands derive both from their palette |
| Ink | `#0B0B0C` | `#FFFFFF` or bone | Never pure black on white for large areas of text |
| Muted | `#6B6B70` | `#8E8E94` | Eyebrows, meta, secondary links; measure contrast |
| Rule | ink at 12% | white at 16% | Hairlines, underline base, hover fills |
| Accent | brand primary | same, checked on dark | Primary action only |

| Text role | Size | Weight | Tracking | Line height |
| --- | --- | --- | --- | --- |
| Display or wordmark | `clamp(88px, 14vw, 208px)` | 800 | `-.05em` | `.84-.85` |
| Promise or tagline | `clamp(18px, 1.6vw, 24px)` | 300 | 0 | `1.33-1.35` |
| Body prose | 16-18px | 400 | 0 | 1.5-1.6 |
| Button | 16-18px | 600 | 0 | 1 |
| Eyebrow | 11-13px uppercase | 500 | `.14-.18em` | 16px |

## CSS starter

```css
:root {
  --ground: #FFFFFF; --ink: #0B0B0C; --muted: #6B6B70; --rule: rgb(11 11 12 / .12);
  --accent: #FF2BD1; --on-accent: #0B0B0C;
  --pad: 96px;
  --ease-out: cubic-bezier(.23, 1, .32, 1);
  --ease-in-out: cubic-bezier(.77, 0, .175, 1);
  color-scheme: light;
}
@media (prefers-color-scheme: dark) {
  :root { --ground: #0B0B0C; --ink: #FFFFFF; --muted: #8E8E94;
          --rule: rgb(255 255 255 / .16); color-scheme: dark; }
}
@media (max-width: 760px) { :root { --pad: 24px; } }

body { background: var(--ground); color: var(--ink); font-synthesis: none;
       -webkit-font-smoothing: antialiased; }

.eyebrow { font: 500 12px/16px var(--font-mono, ui-monospace, monospace);
           letter-spacing: .14em; text-transform: uppercase; color: var(--muted);
           position: relative; z-index: 1; } /* stays clickable under a tight display */
.display { font-size: clamp(88px, 14vw, 208px); font-weight: 800;
           letter-spacing: -.05em; line-height: .85; text-wrap: balance; }
.promise { font-size: clamp(18px, 1.6vw, 24px); font-weight: 300;
           line-height: 1.33; text-wrap: pretty; }

/* one pill action */
.cta { display: inline-flex; align-items: center; gap: 12px; height: 54px;
       padding: 0 26px 0 28px; border-radius: 999px; font-weight: 600;
       background: var(--accent); color: var(--on-accent);
       transition: scale 160ms ease-out; }
.cta svg { transition: translate 160ms ease-out; }
.cta:active { scale: .97; }
@media (hover: hover) and (pointer: fine) { .cta:hover svg { translate: 2px 0; } }

/* hairline link that wipes to full ink */
.link { position: relative; padding-bottom: 3px;
        background: linear-gradient(currentColor, currentColor) 100% 100% / 0 1px no-repeat,
                    linear-gradient(var(--rule), var(--rule)) 0 100% / 100% 1px no-repeat;
        transition: background-size 350ms var(--ease-out); }
.link::after { content: ""; position: absolute; inset: -12px -10px; } /* 44px target */
@media (hover: hover) and (pointer: fine) {
  .link:hover { background-position-x: 0; background-size: 100% 1px, 100% 1px; }
}
:is(.cta, .link):focus-visible { outline: 2px solid var(--ink); outline-offset: 3px; }

/* entrance: chunks rise in with a short stagger (set --i per chunk) */
.rise { animation: rise 480ms var(--ease-out) both;
        animation-delay: calc(var(--i, 0) * 80ms + 100ms); }
@keyframes rise { from { opacity: 0; translate: 0 10px; filter: blur(4px); } }

@media (prefers-reduced-motion: reduce) {
  .rise { animation-name: fade; }
  @keyframes fade { from { opacity: 0; } }
  .cta, .cta svg, .link { transition: none; }
}
```

The dark block above follows the system only. A site with a manual theme toggle
needs `data-theme` overrides as well; see [colors](../better-colors.md).

## Texture snippets

- Print grain on a colored surface: an inline SVG `feTurbulence` fractal noise as a
  `background-image` with `background-blend-mode: soft-light`, tiled at ~180px.
- Hairline chroma on a wordmark: two `text-shadow` offsets of
  `clamp(.5px, .006em, 2px)` in two brand hues at ~.7-.8 alpha; drop it on
  `::selection`.
- Dot grid in an empty area: `radial-gradient(circle, var(--rule) 1px, transparent 1.5px)`
  at a 24px tile, kept clear of copy.

## Before shipping

- Name the one gesture and the one accent; remove a second of either.
- Check that the signature comes from this brand and still reads as a still image.
- Turn effects off; the page must still communicate and convert.
- Render light and dark if both exist, desktop and phone, and check click targets
  around the display line box.
- Ship the 404, favicon, touch icon, OG image and `theme-color`.
