# Accessibility

Most accessibility is free if you use the platform: native elements ship with
keyboard support, real labels announce themselves and a visible focus ring is one CSS
rule. Report each failure against the WCAG 2.2 criterion it breaks, and write fixes
in the project's styling system and components. Review in two walks: keyboard-only,
completing every flow without a mouse, then screen-reader, hearing every control's
name, role and state. Prefer platform defaults and less ARIA.

Elsewhere: contrast measurement and color in [better-colors](better-colors.md); text
sizing, iOS input zoom and `lang` in [better-typography](better-typography.md); RTL
and sticky-chrome scroll padding in [better-layout](better-layout.md); label, error
and alt-text wording in [better-writing](better-writing.md); sticky hover, tap
feedback and `touch-action` in [better-pwa](better-pwa.md); motion in
[better-animation](better-animation.md); sound in [better-sound](better-sound.md).

## Separate criteria from conventions

A finding cites a WCAG 2.2 Level A or AA criterion by number, or a concrete task an
assistive-technology user cannot complete. Everything else is a recommendation and
never `HIGH`: AAA criteria, APG conventions, the one-`<h1>` and no-skipped-levels
outline, and the 44px and 40px targets. Thresholds below are exact; the larger
targets are heuristics that yield to an established, usable density.

## Semantics and names

Open [semantics-and-aria.md](better-accessibility/semantics-and-aria.md) for ARIA
rules and misuse, button versus link, landmarks, name precedence and disabled states.

- **Native elements first.** `<button>` for actions, `<a href>` for navigation, never
  a `<div>` with a click handler; a real link supports Cmd/Ctrl/middle-click. No ARIA
  beats bad ARIA, and a role promises the full keyboard model.
- **Disabled means unavailable.** Use native `disabled` for a truly unavailable
  control, and `aria-disabled="true"` only when it should stay focusable, blocking
  its behavior in code and styling the state. A tooltip on a natively disabled
  control never reaches keyboard or touch users; put the reason in visible text.
- **Accessible names everywhere.** Icon-only buttons need a descriptive `aria-label`,
  and visible label text must appear in the accessible name (2.5.3). Decorative
  elements get `aria-hidden="true"`, never on or above a focusable element.
- **Structure is navigation.** Headings describe sections in a coherent outline under
  one visible `<main>`; after repeated chrome, a "Skip to content" link leads focus.

## Focus and keyboard

Open [focus-and-keyboard.md](better-accessibility/focus-and-keyboard.md) for ring and
forced-colors recipes, skip links, roving tabindex, key maps, trapping and routing.

- **Visible focus rings.** Style `:focus-visible`, not bare `:focus`, preferring the
  browser default. A custom ring uses an explicit color or token at 3:1 against every
  adjacent color, `currentColor` included (1.4.11); a `2px` solid perimeter at a 3:1
  change is the 2.4.13 AAA target. Never `outline: none` without a verified
  replacement; build rings from `outline`, as forced colors drops `box-shadow`.
- **Focus is never obscured** fully by a sticky header, footer or banner (2.4.11).
- **Full keyboard support.** Every pointer interaction needs a keyboard path. Follow
  the APG: Escape closes overlays, arrows move within composites, Tab between them,
  Enter and Space activate buttons. Use only `tabindex` `0` and `-1`; composites use
  roving tabindex, or `aria-activedescendant` where focus stays in an input.
- **Trap and restore focus.** Prefer `<dialog>` opened with `showModal()`; a custom
  overlay sets `inert` on the background and `role="dialog"` with `aria-modal="true"`
  on itself. Move focus in on open and back to the trigger on close. On client-side
  route changes, update `document.title` and focus the new view's `<h1>` or `<main>`.

## Targets, forms and announcements

Open [hit-areas.md](better-accessibility/hit-areas.md) for target sizes, exceptions
and collisions; [forms.md](better-accessibility/forms.md) for labels, errors,
autocomplete and submit; [screen-readers.md](better-accessibility/screen-readers.md)
for hidden text, live regions, alt text, SVG and media.

- **Minimum hit area.** 2.5.8 AA requires 24×24 CSS pixels or an exception; aim for
  44×44px on touch and 40×40px on desktop if density permits. Extend small visuals
  with a pseudo-element, never overlap extended areas, and give decorative layers
  `pointer-events: none`. Every drag needs a single-pointer alternative (2.5.7).
- **Label and type every control.** Every input gets a `<label for>` or a wrapping
  `<label>`, sharing one hit target with its control; a placeholder is never a label.
  Add a valid `autocomplete` token, a descriptive `name`, and the `type` and
  `inputmode` that summon the right keyboard. Never block paste (3.3.8).
- **Errors that announce.** Validate on submit, never by disabling submit until the
  form is valid. Mark failing fields `aria-invalid="true"`, point `aria-describedby`
  at the inline error text and focus the first invalid field.
- **Announce dynamic content.** `aria-describedby` carries field validation;
  `role="status"` (polite) carries non-urgent updates tied to no control, like toasts
  and counts; `role="alert"` carries only urgent untied errors. Render the empty
  region before updating its text; test inserted alerts on target screen readers.
- **Alt text by purpose.** Decorative images get `alt=""`, informative ones describe
  meaning, functional ones the action. Video needs captions and audio a transcript.
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

Inactive controls and logos are exempt. Measure rendered pairs with
[better-colors](better-colors.md); report each failure's criterion.

## Motion, timers and zoom

Open [motion-and-zoom.md](better-accessibility/motion-and-zoom.md) for reduced-motion
recipes, the disable/replace/keep table, timed UI, reflow and units.

- **Honor prefers-reduced-motion.** Wrap motion in `@media (prefers-reduced-motion:
  no-preference)` (opt-in); otherwise cross-fade, don't slide or scale, and drop
  parallax and autoplay. Reduced is gentler, not zero: keep feedback and state cues.
- **Nothing the user needs runs on a timer.** Anything moving, blinking or updating
  on its own for more than 5 seconds needs a visible pause control (2.2.2). Toasts
  carrying an action, such as Undo, or an error stay until dismissed.
- **Survive zoom and text resize.** Text survives 200% resize (1.4.4); pages reflow
  at 320px without horizontal scrolling (1.4.10). Text containers take `min-height`,
  not `height` (more in [better-layout](better-layout.md)). Never set
  `maximum-scale=1` or `user-scalable=no`; iOS input zoom takes
  [better-typography](better-typography.md)'s `16px` rule.

## Before you finish

| Pattern | Fix |
| --- | --- |
| `outline: none`/`outline-none` with no `focus-visible` replacement, or a ring only from `box-shadow` (utility `ring` classes) | Restore the browser ring or a verified `outline` one; add a transparent outline for forced colors |
| A click handler (`onClick`, `@click`, `addEventListener`) on a `<div>` or `<span>`, or `role="button"`/`"tab"` with no key handler | `<button>`, `<a href>` when it navigates, or the APG key map |
| Any positive `tabindex` | Fix the DOM order and use `0` |
| Submit disabled until the form is valid | Keep it enabled and validate on submit |
| A live region inserted with its text, or `aria-live="assertive"` on a success toast | Render the empty region first, then its text; `role="status"` for toasts |
| A toast with an action or an error on a dismiss timer | Keep it until dismissed |
| `<img` with no `alt` attribute | `alt=""` if decorative, otherwise describe the purpose |
| `aria-label` whose text omits the visible label | Start the name with the visible text |
| `aria-hidden` on an ancestor of a button, link or input | Remove it, or make the subtree `inert` |
| `maximum-scale=1` or `user-scalable=no` | Remove it |
| A paste handler that prevents default on a password or code field | Remove it |
| A drag handler or drag library with no button alternative | Add a single-pointer path to the same result |

## Reporting

**Severity.** Most [escalation triggers](better-ui-review.md#rank-by-user-impact) are
this domain's and `HIGH` on sight. Recommendations are never `HIGH` ([criteria versus
conventions](#separate-criteria-from-conventions)); otherwise rank by impact.

**Verification.** Without a browser: accessible names, keyboard handlers on
non-native controls, focus styles, reduced-motion guards and bound form labels. With
one: tab the flow, read names and roles from the accessibility tree, confirm visible
focus at each stop, run an automated audit. Mark unrun checks `Not verified`.

For reviews, use [the shared review format](better-ui-review.md#report), naming the
WCAG criterion beside the user impact.
