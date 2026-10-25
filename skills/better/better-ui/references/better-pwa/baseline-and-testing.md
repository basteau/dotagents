# Baseline and testing

A starter floor for new mobile-facing apps, a self-check before finishing, and the
device workflow. In an existing project, adopt only the lines whose reason applies.

## Baseline

```html
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover" />
<meta name="theme-color" media="(prefers-color-scheme: light)" content="#ffffff" />
<meta name="theme-color" media="(prefers-color-scheme: dark)" content="#0a0a0a" />
```

```css
html {
  -webkit-text-size-adjust: 100%;
}

input, textarea, select {
  font-size: max(16px, 1rem); /* or a sizing approach from better-typography */
}

button, a, [role="button"], summary, label {
  touch-action: manipulation;
}

button, [role="button"], summary {
  -webkit-user-select: none;
  user-select: none;
}

/* Once every tappable element has an :active state */
html { -webkit-tap-highlight-color: transparent; }

/* App-like shells only; scrolling documents keep pull-to-refresh */
html { overscroll-behavior-y: none; }

@media (hover: hover) and (pointer: fine) {
  /* hover rules live here */
}
```

Add `interactive-widget=resizes-content` for keyboard-heavy shells, `dvh`/`svh`
per surface, and safe-area padding on fixed chrome as described in
[viewport-and-scroll.md](viewport-and-scroll.md).

## Never ship

| Never | Instead |
| --- | --- |
| `user-scalable=no` or `maximum-scale=1` | `16px` computed input text; user zoom intact |
| Ungated `:hover` | `@media (hover: hover)`, plus `(pointer: fine)` where precision matters |
| Press feedback only on `click` | `:active` or `pointerdown`, activation still on `click` |
| Tap highlight removed with no press state | Remove it only where `:active` replaces it |
| `100vh` app shell or bottom-pinned UI | `100dvh` |
| `100dvh` marketing hero | `100svh` |
| `env(safe-area-inset-*)` without `viewport-fit=cover` | Add the viewport value or every inset is `0` |
| Safe-area inset added twice in nested chrome | One inset per edge |
| `user-select: none` on `body` or prose | Only on control labels and handles |
| `touch-action: none` on content people scroll past | `pan-x` or `pan-y` on the gesture surface |
| `touchmove` plus `preventDefault()` for overscroll | `overscroll-behavior` |
| One `theme-color` for both schemes, or the brand color | One per scheme, matching the top surface |
| User-agent or width sniffing for touch | `hover`, `pointer`, `any-pointer` media queries |
| Drag-only actions | A button or keyboard alternative |
| "Fixed" from desktop emulation | Confirmed on a phone, or named as unverified |

## On a device

1. Serve the dev server on the LAN (`0.0.0.0` or the framework's host flag) and open
   the machine's IP on the phone, on the same network.
2. Inspect remotely. iOS: enable Web Inspector in the phone's Safari advanced
   settings, then Safari's Develop menu on the Mac. Android: enable USB debugging,
   then `chrome://inspect` in desktop Chrome.
3. Service workers, install prompts, and some device APIs need a secure context.
   `localhost` qualifies; a LAN IP over `http` does not. Use the project's HTTPS dev
   option or a tunnel when those features are under test.
4. The iOS Simulator and Android Emulator catch layout, safe-area, and keyboard
   issues better than desktop emulation, but neither establishes touch feel.

## Checklist

Run the affected flow, not the whole app:

- Tap, press-and-hold, and slide off a control: feedback on contact, no stuck hover,
  no flash, no label selection, the action cancels.
- Scroll to load the page, then collapse and expand the URL bar: no jump, bottom
  actions visible.
- Focus every field: no zoom, correct keyboard and return key, submit reachable with
  the keyboard open, layout restored after dismissal.
- Portrait and landscape: insets on all edges, nothing under the notch or home
  indicator, no doubled padding.
- Scroll inner containers to both ends: the page behind stays still, pull-to-refresh
  only where intended.
- Swipe each gesture surface diagonally and pinch-zoom over it: one owner wins,
  cancelled gestures fire nothing.
- Switch system and in-app theme: status bar and browser chrome follow.
- Installed mode, when targeted: launch screen color, status bar, safe areas, in-app
  back navigation.
- An older phone, when performance or tap latency is the complaint.

Report which items ran on hardware, which in a simulator, and which remain unverified.
