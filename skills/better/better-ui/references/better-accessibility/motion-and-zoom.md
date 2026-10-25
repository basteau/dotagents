# Motion and zoom

`prefers-reduced-motion`, timed UI, zoom and reflow, and units that respect user settings. Animation recipes, durations and the reduced-motion version of each pattern belong to [better-animation](../better-animation.md).

## prefers-reduced-motion

Wrap animations in `@media (prefers-reduced-motion: no-preference)`, so users who asked for reduced motion get the static version without an override per animation.

```css
/* Good: motion is opt-in */
.card {
  /* static styles */
}
@media (prefers-reduced-motion: no-preference) {
  .card {
    transition: transform 200ms ease-out;
  }
}
```

For an existing codebase where opt-in isn't feasible, the global kill switch is the fallback:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

`0.01ms` rather than `none`, so `animationend` and `transitionend` still fire and JavaScript waiting on them doesn't hang. JavaScript-driven motion (scroll handlers, canvas, animation libraries) ignores CSS, so read `matchMedia("(prefers-reduced-motion: reduce)")` and listen for its `change` event.

### What to disable vs reduce

Reduced motion targets vestibular triggers, not feedback.

| Disable entirely | Replace | Keep |
| --- | --- | --- |
| Parallax scrolling | Slide/scale/zoom transitions → opacity cross-fade | Loading spinners and progress |
| Autoplaying video, GIFs, looping decoration | Smooth scrolling → instant jump | Instant state changes (hover color, focus ring) |
| Spinning, large-scale movement across the screen | Auto-rotating carousels → start paused | Brief functional feedback (button press) |

Nothing flashes more than three times per second (2.3.1), with or without the preference.

## Autoplay and timed UI

- **Visible controls on anything that moves on its own** (2.2.2). Anything moving, blinking or updating on its own for more than 5 seconds needs a visible pause or stop control, muted looping hero videos included.
- **Explicit dismissal over timers.** Auto-dismissal suits low-stakes confirmations and nothing else. A toast carrying an action such as Undo, an error or information the user may need stays until dismissed.
- **Pause on attention.** Where a toast must time out, give it at least 5 seconds, and pause the timer while it is hovered or focused and while the tab is hidden (`document.visibilityState`).
- **Never put critical information only in a timed element.** A vanished toast with the only link to an undo action is data loss on a schedule.
- **Warn before a session expires** (2.2.1), and let the user extend it with a single action.

## Zoom and reflow

- **200% text resize** (1.4.4). All content and functionality survives text scaled to 200%.
- **Reflow at 320px** (1.4.10). At 400% zoom on a 1280px viewport, equivalent to a 320px one, the page must work with vertical scrolling alone. Genuinely 2D content is the exception: tables, maps and code blocks scroll inside their own container.
- **Text spacing** (1.4.12). Content survives line height 1.5, paragraph spacing 2em, letter spacing 0.12em and word spacing 0.16em without clipping. Fixed heights are what break, so text containers take `min-height` and grow.

### rem vs px

Respect how the codebase is set up. Where the project sizes in `px` or on an established utility scale, stay consistent and never introduce mixed units into someone else's system. Where you have the choice, `rem` respects the user's base font size and `px` ignores it: `rem` for font sizes, text-container widths and spacing that should scale with text; `px` for borders, hairlines, focus outline width and offset, and fixed decorations.

Write media queries in `rem` or `em` where the codebase allows. At a larger browser font size such a query switches to the narrow layout when the text needs it, and a `px` query does not. Breakpoint placement belongs to [better-layout](../better-layout.md).
