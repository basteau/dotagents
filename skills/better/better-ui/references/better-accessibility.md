# Accessibility

Most accessibility is free if you use the platform. Native elements ship with keyboard support, real labels announce themselves and a visible focus ring is one CSS rule. This reference covers semantics, keyboard and focus, accessible names, forms, announcements, motion and zoom, and reports each failure against the WCAG 2.2 criterion it breaks.

Write fixes in the project's styling system and components. Reviewing means two walks. Keyboard-only, where every flow completes without a mouse. Then screen-reader, where every control announces a name, a role and its state. When unsure, take the platform default over a custom rebuild, and remove ARIA rather than add it.

Contrast measurement and color fixes belong to [better-colors](better-colors.md). Text sizing, iOS input zoom and language metadata belong to [better-typography](better-typography.md). Spatial RTL and sticky-chrome scroll padding belong to [better-layout](better-layout.md). Label, error and alt-text wording belong to [better-writing](better-writing.md). Sticky hover, tap feedback and `touch-action` belong to [better-pwa](better-pwa.md). Motion recipes belong to [better-animation](better-animation.md); sound cues and their accessibility belong to [better-sound](better-sound.md).

## Separate criteria from conventions

A finding cites a WCAG 2.2 Level A or AA criterion by number, or a concrete task an assistive-technology user cannot complete. Everything else is a recommendation and never `HIGH`: AAA criteria, APG conventions, the one-`<h1>` and no-skipped-levels outline, and the 44px and 40px targets. Criterion thresholds below are exact; the larger targets are heuristics, so keep an established, usable density.

## Native elements first

The first rule of ARIA: don't use ARIA when a native element exists. `<button>` for actions, `<a href>` for navigation, never a `<div>` with a click handler. A real link must support Cmd/Ctrl/middle-click. No ARIA is better than bad ARIA, and a role is a promise of the full keyboard model. See [semantics-and-aria.md](better-accessibility/semantics-and-aria.md) for landmarks and button-vs-link.

## Disabled means unavailable

Use native `disabled` when a control is genuinely unavailable. Reach for `aria-disabled="true"` only when it should stay focusable, then block pointer, keyboard and form behavior in code and style the state explicitly. A tooltip on a natively disabled control never opens for keyboard or touch users; put the reason in visible text. Rules are in [semantics-and-aria.md](better-accessibility/semantics-and-aria.md#disabled-states).

## Visible focus rings

Style `:focus-visible`, not bare `:focus`. Keyboard users get a ring and mouse users usually don't. Prefer the browser's unmodified indicator.

A custom ring needs a project focus token or another explicit color, and must reach 3:1 against every adjacent color it crosses, `currentColor` included (1.4.11). A `2px` solid perimeter with a 3:1 change of contrast is the 2.4.13 AAA target. Never use `outline: none` without a verified replacement. Build rings from `outline`, not `box-shadow`, which forced-colors mode removes.

A focused element must never sit fully hidden behind a sticky header, footer or banner (2.4.11). Recipes are in [focus-and-keyboard.md](better-accessibility/focus-and-keyboard.md).

## Full keyboard support

Every pointer interaction needs a keyboard path. Follow the ARIA APG patterns: Escape closes overlays, arrow keys move within composite widgets, Tab moves between widgets, Enter and Space activate buttons.

Use only `tabindex="0"` to join the natural tab order and `tabindex="-1"` for programmatic focus. Positive values break that order. Composite widgets use roving tabindex, where the active item is `0` and every other is `-1`. Where focus must stay in an input, as in a combobox, use `aria-activedescendant` instead. Key maps per widget are in [focus-and-keyboard.md](better-accessibility/focus-and-keyboard.md#keyboard-patterns-aria-apg).

## Trap and restore focus

Prefer `<dialog>` opened with `showModal()`, which makes the background inert and handles Escape. A custom overlay sets `inert` on the background, `role="dialog"` and `aria-modal="true"` on itself. Either way, move focus inside on open and return it to the trigger on close.

Client-side route changes reset nothing. Update `document.title` and move focus to the new view's `<h1>` or `<main>`. Both recipes are in [focus-and-keyboard.md](better-accessibility/focus-and-keyboard.md#focus-trapping-and-restoration).

## Minimum hit area

WCAG 2.5.8's Level AA baseline is a 24×24 CSS-pixel target, or one of its exceptions. Aim for 44×44px on touch and 40×40px on desktop where density permits. Extend with a pseudo-element when the visible element should stay smaller, and never let extended hit areas overlap. Give decorative layers `pointer-events: none`, so a glow never swallows the clicks meant for the control beneath it.

Every drag needs a single-pointer alternative, such as buttons or a menu that move the same item (2.5.7). Sizes, exceptions and collision rules are in [hit-areas.md](better-accessibility/hit-areas.md).

## Label and type every control

Every input gets a `<label for>` or a wrapping `<label>`. A placeholder is never a label. Label and control share one hit target, with no dead zone between a checkbox and its text.

Add a valid `autocomplete` token and a descriptive `name`, plus the `type` and `inputmode` that summon the right keyboard. Never block paste; users paste passwords and one-time codes (3.3.8). See [forms.md](better-accessibility/forms.md).

## Errors that announce

Validate on submit, never by disabling submit until the form is valid. Mark failing fields `aria-invalid="true"`, point `aria-describedby` at the inline error text and focus the first invalid field. Submit behavior while the request runs is in [forms.md](better-accessibility/forms.md#submit-behavior).

## Accessible names everywhere

Icon-only buttons need a descriptive `aria-label`. Visible label text must appear in the accessible name (2.5.3). Decorative elements get `aria-hidden="true"`, never on or above a focusable element.

## Pair color with a second cue

Status needs a redundant cue: an icon, text or an underline alongside the color (1.4.1). This reference decides which contrast requirement applies:

| Criterion | Applies to | Minimum |
| --- | --- | --- |
| 1.4.3 AA | Text | 4.5:1 |
| 1.4.3 AA | Large text, at least `24px` or `18.67px` bold | 3:1 |
| 1.4.11 AA | UI component boundaries, states and meaningful graphics | 3:1 |

Inactive controls and logos are exempt. Use [better-colors](better-colors.md) to measure the rendered pair. When it fails, report the pair and the criterion it misses, and change colors only when fixes are in scope.

## Honor prefers-reduced-motion

Wrap motion in `@media (prefers-reduced-motion: no-preference)` so it is opt-in. Under reduced motion, replace slides and scales with opacity cross-fades, and kill parallax and autoplay entirely. Reduced means gentler, not zero: keep feedback and state changes. See [motion-and-zoom.md](better-accessibility/motion-and-zoom.md).

## Nothing the user needs runs on a timer

Anything moving, blinking or updating on its own for more than 5 seconds needs a visible pause control (2.2.2). Toasts carrying an action, such as Undo, or an error stay until dismissed. Rules are in [motion-and-zoom.md](better-accessibility/motion-and-zoom.md#autoplay-and-timed-ui).

## Announce dynamic content

Three mechanisms, three jobs. `aria-describedby` carries field-specific validation. A polite live region (`role="status"`) carries non-urgent updates not tied to a control, such as toasts and result counts. `role="alert"` carries urgent untied errors and nothing else.

Repeated polite announcements need a stable empty region rendered before its text updates. Dynamically inserted alerts vary in support, so test them on the screen readers you target. See [screen-readers.md](better-accessibility/screen-readers.md).

## Alt text by purpose

Decorative images get `alt=""`. Informative images describe the meaning. Functional images describe the action, never the picture. Video needs captions and audio a transcript. The full table is in [screen-readers.md](better-accessibility/screen-readers.md#alt-text).

## Never make sound the only cue

Every audio cue has a visual equivalent, and sound stays optional. Sound design, volume and preference handling are in [better-sound](better-sound.md).

## Structure is navigation

Use headings that describe their sections and form a coherent outline. Expose one visible primary `<main>` landmark. When repeated navigation or chrome precedes it, make a "Skip to content" link the first focusable element. Anchored headings get `scroll-margin-block-start`.

## Survive zoom and text resize

Text must survive 200% resize (1.4.4), and the page must reflow at 320px width without horizontal scrolling (1.4.10). Text containers take `min-height`, not `height`; [better-layout](better-layout.md) owns the rest of that fix. Never set `maximum-scale=1` or `user-scalable=no` in the viewport meta; fix iOS input zoom with [better-typography](better-typography.md)'s `16px` rule instead.

## Before you finish

| Pattern | Fix |
| --- | --- |
| `outline: none` or `outline-none` with no `focus-visible` replacement nearby | Restore the browser ring or add a verified custom one |
| Focus ring built only from `box-shadow`, including utility `ring` classes | Use `outline`, or add a transparent outline for forced colors |
| A click handler on a `<div>` or `<span>` (`onClick`, `@click`, `addEventListener`) | Use `<button>`, or `<a href>` when it navigates |
| `role="button"` or `role="tab"` with no key handler | Use the native element, or implement the APG key map |
| Any positive `tabindex` | Fix the DOM order and use `0` |
| Submit disabled until the form is valid | Keep it enabled and validate on submit |
| A live region inserted together with its text | Render the empty region first and update its text |
| `aria-live="assertive"` on a success toast | `role="status"` |
| `<img` with no `alt` attribute | `alt=""` if decorative, otherwise describe the purpose |
| `aria-label` whose text omits the visible label | Start the name with the visible text |
| `aria-hidden` on an ancestor of a button, link or input | Remove it, or make the subtree `inert` |
| `maximum-scale=1` or `user-scalable=no` | Remove it |
| A paste handler that prevents default on a password or code field | Remove it |
| A drag handler or drag library with no button alternative | Add a single-pointer path to the same result |

## Reporting

**Severity.** `HIGH` on sight: a missing accessible name, a missing focus indicator, a pointer path with no keyboard path, motion ignoring reduced motion, loss at 320px or 200% and meaning carried by color alone. Otherwise rank by user impact as in [better-ui-review](better-ui-review.md).

**Verification.** Without a browser: accessible names on every interactive element, keyboard handlers on non-native controls, focus styles, `prefers-reduced-motion` guards and form labels bound to their inputs. With one: tab the flow in order, read computed names and roles from the accessibility tree, confirm a visible focus indicator at every stop and run an automated audit. Report every check you could not run as `Not verified`.

For reviews, use the report format in [better-ui-review](better-ui-review.md), and name the WCAG criterion alongside the user impact.
