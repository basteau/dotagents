# Accessibility

Most accessibility is free if you use the platform: native elements ship with
keyboard support, real labels announce themselves and a visible focus ring is one CSS
rule. Report each failure against the WCAG 2.2 criterion it breaks.

Write fixes in the project's styling system and components. Reviewing means two
walks. Keyboard-only, where every flow completes without a mouse. Then screen-reader,
where every control announces a name, a role and its state. When unsure, take the
platform default over a custom rebuild, and remove ARIA rather than add it.

Contrast measurement and color fixes belong to [better-colors](better-colors.md).
Text sizing, iOS input zoom and language metadata belong to
[better-typography](better-typography.md). Spatial RTL and sticky-chrome scroll
padding belong to [better-layout](better-layout.md). Label, error and alt-text
wording belong to [better-writing](better-writing.md). Sticky hover, tap feedback and
`touch-action` belong to [better-pwa](better-pwa.md). Motion recipes belong to
[better-animation](better-animation.md); sound cues and their accessibility belong to
[better-sound](better-sound.md).

## Separate criteria from conventions

A finding cites a WCAG 2.2 Level A or AA criterion by number, or a concrete task an
assistive-technology user cannot complete. Everything else is a recommendation and
never `HIGH`: AAA criteria, APG conventions, the one-`<h1>` and no-skipped-levels
outline, and the 44px and 40px targets. Criterion thresholds below are exact; the
larger targets are heuristics, so keep an established, usable density.

## Semantics and names

Open [semantics-and-aria.md](better-accessibility/semantics-and-aria.md) for the
rules of ARIA, button versus link, landmarks, name precedence, common ARIA mistakes
and disabled states.

- **Native elements first.** Don't use ARIA when a native element exists: `<button>`
  for actions, `<a href>` for navigation, never a `<div>` with a click handler. A
  real link supports Cmd/Ctrl/middle-click. No ARIA is better than bad ARIA, and a
  role is a promise of the full keyboard model.
- **Disabled means unavailable.** Use native `disabled` for a control that is
  genuinely unavailable, and `aria-disabled="true"` only when it should stay
  focusable, then block its behavior in code and style the state. A tooltip on a
  natively disabled control never opens for keyboard or touch users; put the reason
  in visible text.
- **Accessible names everywhere.** Icon-only buttons need a descriptive `aria-label`,
  and visible label text must appear in the accessible name (2.5.3). Decorative
  elements get `aria-hidden="true"`, never on or above a focusable element.
- **Structure is navigation.** Headings describe their sections and form a coherent
  outline, under one visible primary `<main>` landmark. When repeated chrome precedes
  it, a "Skip to content" link is the first focusable element.

## Focus and keyboard

Open [focus-and-keyboard.md](better-accessibility/focus-and-keyboard.md) for ring and
forced-colors recipes, the skip link, roving tabindex, APG key maps, focus trapping
and route changes.

- **Visible focus rings.** Style `:focus-visible`, not bare `:focus`, and prefer the
  browser's unmodified indicator. A custom ring uses a project focus token or another
  explicit color and reaches 3:1 against every adjacent color it crosses,
  `currentColor` included (1.4.11); a `2px` solid perimeter with a 3:1 change of
  contrast is the 2.4.13 AAA target. Never use `outline: none` without a verified
  replacement, and build rings from `outline`, not `box-shadow`, which forced-colors
  mode removes.
- **Focus is never obscured.** A focused element never sits fully hidden behind a
  sticky header, footer or banner (2.4.11).
- **Full keyboard support.** Every pointer interaction needs a keyboard path. Follow
  the ARIA APG patterns: Escape closes overlays, arrow keys move within composite
  widgets, Tab moves between widgets, Enter and Space activate buttons. Use only
  `tabindex="0"` and `tabindex="-1"`; composite widgets use roving tabindex, or
  `aria-activedescendant` where focus must stay in an input.
- **Trap and restore focus.** Prefer `<dialog>` opened with `showModal()`; a custom
  overlay sets `inert` on the background and `role="dialog"` with `aria-modal="true"`
  on itself. Move focus inside on open and return it to the trigger on close.
  Client-side route changes reset nothing: update `document.title` and move focus to
  the new view's `<h1>` or `<main>`.

## Targets, forms and announcements

Open [hit-areas.md](better-accessibility/hit-areas.md) for target sizes, exceptions
and collision rules; [forms.md](better-accessibility/forms.md) for labels, error
markup, autocomplete tokens, input types and submit behavior;
[screen-readers.md](better-accessibility/screen-readers.md) for visually hidden text,
live regions, the alt-text table, SVG and media.

- **Minimum hit area.** WCAG 2.5.8's Level AA baseline is a 24×24 CSS-pixel target,
  or one of its exceptions; aim for 44×44px on touch and 40×40px on desktop where
  density permits. Extend a smaller visual with a pseudo-element, never let extended
  hit areas overlap, and give decorative layers `pointer-events: none`. Every drag
  needs a single-pointer alternative (2.5.7).
- **Label and type every control.** Every input gets a `<label for>` or a wrapping
  `<label>`, sharing one hit target with its control; a placeholder is never a label.
  Add a valid `autocomplete` token, a descriptive `name`, and the `type` and
  `inputmode` that summon the right keyboard. Never block paste (3.3.8).
- **Errors that announce.** Validate on submit, never by disabling submit until the
  form is valid. Mark failing fields `aria-invalid="true"`, point `aria-describedby`
  at the inline error text and focus the first invalid field.
- **Announce dynamic content.** `aria-describedby` carries field-specific validation;
  a polite live region (`role="status"`) carries non-urgent updates not tied to a
  control, such as toasts and result counts; `role="alert"` carries urgent untied
  errors and nothing else. Render a stable empty region before its text updates, and
  test dynamically inserted alerts on the screen readers you target.
- **Alt text by purpose.** Decorative images get `alt=""`, informative images
  describe the meaning, functional images describe the action. Video needs captions
  and audio a transcript.
- **Never make sound the only cue.** Every audio cue has a visual equivalent, and
  sound stays optional; the rest is [better-sound](better-sound.md)'s.

## Pair color with a second cue

Status needs a redundant cue: an icon, text or an underline alongside the color
(1.4.1). This reference decides which contrast requirement applies:

| Criterion | Applies to | Minimum |
| --- | --- | --- |
| 1.4.3 AA | Text | 4.5:1 |
| 1.4.3 AA | Large text, at least `24px` or `18.67px` bold | 3:1 |
| 1.4.11 AA | UI component boundaries, states and meaningful graphics | 3:1 |

Inactive controls and logos are exempt. Use [better-colors](better-colors.md) to
measure the rendered pair. When it fails, report the pair and the criterion it
misses, and change colors only when fixes are in scope.

## Motion, timers and zoom

Open [motion-and-zoom.md](better-accessibility/motion-and-zoom.md) for the
reduced-motion recipes, the disable, replace and keep table, timed UI, reflow and
units.

- **Honor prefers-reduced-motion.** Wrap motion in `@media (prefers-reduced-motion:
  no-preference)` so it is opt-in. Under reduced motion, replace slides and scales
  with opacity cross-fades, and kill parallax and autoplay entirely. Reduced means
  gentler, not zero: keep feedback and state changes.
- **Nothing the user needs runs on a timer.** Anything moving, blinking or updating
  on its own for more than 5 seconds needs a visible pause control (2.2.2). Toasts
  carrying an action, such as Undo, or an error stay until dismissed.
- **Survive zoom and text resize.** Text survives 200% resize (1.4.4), and the page
  reflows at 320px width without horizontal scrolling (1.4.10). Text containers take
  `min-height`, not `height`; [better-layout](better-layout.md) owns the rest of that
  fix. Never set `maximum-scale=1` or `user-scalable=no`; fix iOS input zoom with
  [better-typography](better-typography.md)'s `16px` rule instead.

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
| A toast with an action or an error on a dismiss timer | Keep it until dismissed |
| `<img` with no `alt` attribute | `alt=""` if decorative, otherwise describe the purpose |
| `aria-label` whose text omits the visible label | Start the name with the visible text |
| `aria-hidden` on an ancestor of a button, link or input | Remove it, or make the subtree `inert` |
| `maximum-scale=1` or `user-scalable=no` | Remove it |
| A paste handler that prevents default on a password or code field | Remove it |
| A drag handler or drag library with no button alternative | Add a single-pointer path to the same result |

## Reporting

**Severity.** Most [escalation triggers](better-ui-review.md#rank-by-user-impact)
belong to this domain and are `HIGH` on sight. Recommendations are never `HIGH`
([criteria versus conventions](#separate-criteria-from-conventions)); otherwise rank
by user impact.

**Verification.** Without a browser: accessible names on every interactive element,
keyboard handlers on non-native controls, focus styles, `prefers-reduced-motion`
guards and form labels bound to their inputs. With one: tab the flow in order, read
computed names and roles from the accessibility tree, confirm a visible focus
indicator at every stop and run an automated audit. Report every check you could not
run as `Not verified`.

For reviews, use [the shared review format](better-ui-review.md#report), naming the
WCAG criterion beside the user impact.
