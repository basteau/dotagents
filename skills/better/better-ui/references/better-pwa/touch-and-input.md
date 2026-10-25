# Touch and input

Recipes for hover, press feedback, tap delay, tap highlight, selection, fields, the
software keyboard, and haptics. Replace example values with the project's tokens.

## Hover gated by capability

```css
@media (hover: hover) and (pointer: fine) {
  .button:hover {
    background: var(--surface-hover);
  }
}
```

`(hover: hover)` means the primary input can hover; `(pointer: fine)` means it is
precise. Use the first alone for hover that is purely color, both when the effect
assumes a mouse (reveal-on-hover, magnetic or scale effects). These queries describe
the primary input only; `any-hover` and `any-pointer` describe all attached inputs.
Never hide information or actions behind hover alone: touch needs another path.

Tailwind (optional): v4's `hover:` variant already compiles to `@media (hover: hover)`;
v3 needs `future.hoverOnlyWhenSupported`. Inspect output before adding config.

## Press feedback on contact

```css
.button {
  transition: transform 120ms var(--ease-out), background-color 120ms var(--ease-out);
}
.button:active {
  transform: scale(0.97);
  background: var(--surface-pressed);
}
```

Press feedback appears on contact and the action commits on release, so sliding
off cancels it. Native `click` already behaves this way; keep it for activation.
When JavaScript must own the pressed state:

```js
el.addEventListener('pointerdown', () => el.dataset.pressed = '');
for (const type of ['pointerup', 'pointercancel', 'pointerleave']) {
  el.addEventListener(type, () => delete el.dataset.pressed);
}
el.addEventListener('click', run); // activation stays on click: keyboard and cancel work
```

Expand small targets with hit padding rather than visual size; sizes and the
pseudo-element recipe are in [hit areas](../better-accessibility/hit-areas.md).

## Tap delay

```css
button, a, [role="button"], label, summary, .tappable {
  touch-action: manipulation; /* pan and pinch zoom stay; double-tap zoom goes */
}
```

With `width=device-width` most browsers already skip the classic 300ms wait, so if
taps still feel slow, profile the handler, hydration, and long tasks before blaming
the delay. Any custom double-tap recognizer delays every single tap on that element.

## Tap highlight

```css
/* Only where each control has its own :active state */
.app-controls :is(button, a, [role="button"]) {
  -webkit-tap-highlight-color: transparent;
}
```

Move it to `html` only when every tappable element provides press feedback. The
highlight is unrelated to focus; `:focus-visible` styling stays.

## Selection and long-press callouts

```css
button, [role="button"], [role="tab"], .chip, .drag-handle {
  -webkit-user-select: none; /* Safari still needs the prefix */
  user-select: none;
}
.drag-handle, .icon-button img {
  -webkit-touch-callout: none; /* only where the callout fights the control */
}
```

Leave inline links in prose selectable and their callouts intact. Keep long-press
menus on images people might save or share.

## Fields and keyboards

Focus zoom: iOS zooms when the computed font size of an `input`, `textarea`, or
`select` is under `16px`. `1rem` meets that only at the default root size. Sizing
approaches (size up on mobile, or keep `16px` and scale down) are in
[better-typography](../better-typography.md).

| Field | Attributes |
| --- | --- |
| Email | `type="email" autocomplete="email"` |
| Phone | `type="tel" autocomplete="tel"` |
| One-time code | `inputmode="numeric" autocomplete="one-time-code"` |
| Amount | `inputmode="decimal"` |
| Username, code, URL | `autocapitalize="none" autocorrect="off" spellcheck="false"` |
| Search | `type="search" enterkeyhint="search"` |
| Chat or comment | `enterkeyhint="send"` |
| Last field of a step | `enterkeyhint="done"` or `"next"` to match behavior |

`inputmode="numeric"` suits digit strings that are not quantities (codes, card
numbers); `type="number"` adds spinners and scroll-to-change. Disable correction only
where it harms the value. Keep paste, autofill, and native validation working.

## Keyboard-open layout

```html
<meta name="viewport"
  content="width=device-width, initial-scale=1, viewport-fit=cover, interactive-widget=resizes-content" />
```

`interactive-widget=resizes-content` asks supporting browsers (Chromium-based) to
shrink the layout viewport for the keyboard, so `dvh` shells and bottom-pinned inputs
move with it. Other browsers ignore it and resize only the visual viewport. Choose it
only for keyboard-heavy shells; document pages usually read better without it.

Reach for the Visual Viewport API only after reproducing an obstruction on a device:

```js
const vv = window.visualViewport;
const sync = () => {
  const inset = Math.max(0, window.innerHeight - vv.height - vv.offsetTop);
  document.documentElement.style.setProperty('--keyboard-inset', `${inset}px`);
};
vv?.addEventListener('resize', sync);
vv?.addEventListener('scroll', sync);
```

Use the inset for a bottom composer's offset, not for the whole layout. Verify
focus, typing, autocorrect bar, dismissal, rotation, and the submit action.

`-webkit-text-size-adjust: 100%` on `html` stops landscape text inflation on iOS;
check that the user's text-size settings still apply afterwards.

## Haptics (optional)

```js
function tick() {
  if ('vibrate' in navigator) navigator.vibrate(10);
}
```

Call it in the same handler that changes the visual state, so both land on one
frame. Reserve it for commits, snaps, and errors; never make it the only feedback.
Unsupported browsers, including iOS Safari, simply skip it.
