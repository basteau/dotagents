# Better PWA

Make a mobile web interface respond like an installed app to touch, browser chrome,
and the software keyboard, in a browser tab or installed standalone mode. Most
symptoms are one CSS declaration or meta tag at the platform layer; inspect the
project's browser support, metadata API, scroll containers, and existing primitives
before changing anything.

Motion, springs, and drag physics (velocity, momentum, rubber-banding, thresholds)
belong to [better-animation](better-animation.md) and its
[gestures](better-animation/gestures.md) detail. Hit-area sizes, focus, zoom, and
semantics belong to [better-accessibility](better-accessibility.md); input text
sizing to [better-typography](better-typography.md); responsive structure to
[better-layout](better-layout.md); feedback sounds to [better-sound](better-sound.md).
Service workers, offline caching, install prompts, and push are separate work;
fixing feel does not make an app installable or offline-capable.

## Start from the symptom

| Symptom | Start with |
| --- | --- |
| Hover stays applied after a tap | Gate hover styles by input capability |
| Tap feels laggy | Press feedback on contact; `touch-action: manipulation`; check main-thread work |
| Gray or blue flash on tap | Remove the tap highlight where a press state replaces it |
| Long-press selects a button label or opens a callout | Scope selection and callout suppression to controls |
| Bottom action hidden or layout jumps with the URL bar | `dvh` for app shells, `svh` for first screens |
| Page zooms into a focused field | Computed input font size of at least `16px` |
| Keyboard covers the field or the submit action | Check keyboard viewport behavior before adding JavaScript |
| Notch or home indicator covers controls | `viewport-fit=cover` plus safe-area padding |
| Scrolling a sheet moves or refreshes the page | Contain overscroll on the inner scroller |
| A swipe fights page scrolling | Give the gesture surface one `touch-action` owner |
| Status bar or browser chrome clashes with the page | Per-scheme `theme-color` matching the top surface |
| Installed app strands the user with no way back | Provide in-app navigation in standalone mode |
| Right in desktop emulation, wrong on the phone | Test on real hardware |

Code for every row is in [touch-and-input.md](better-pwa/touch-and-input.md) and
[viewport-and-scroll.md](better-pwa/viewport-and-scroll.md).

## Reach for CSS and metadata before JavaScript

Media queries, `env()`, viewport units, and `touch-action` describe what the device
can do; a touch-detection script or user-agent check guesses. Apply each fix where
its reason applies rather than as a global habit: `user-select: none` is correct on a
button and a defect on body text. A starter baseline for new mobile-facing apps is in
[baseline-and-testing.md](better-pwa/baseline-and-testing.md).

## Gate hover by capability, never by device

Touch browsers fake `:hover` on tap and leave it applied until the next tap
elsewhere. Wrap decorative hover rules in `@media (hover: hover)`, adding
`(pointer: fine)` when the effect assumes precise targeting. Touch, mouse, stylus,
and keyboard can coexist on one device, so keep every path usable; width and
user-agent strings do not identify touch. Check generated CSS first: the framework's
hover variant may already include the guard.

## Give press feedback on contact

Feedback that waits for `click` reads as lag even at 0ms. Style `:active` (keep
`:focus-visible`), or start feedback on `pointerdown` and clear it on release and
`pointercancel`, while activation stays on native `click` so sliding away cancels and
keyboards still work. Keep presses short and never delay the action; scale and timing
are in [better-animation](better-animation/patterns.md#press-feedback).
`touch-action: manipulation` removes double-tap-zoom delay but cannot fix a slow
handler; profile first. Haptics can only echo visible feedback, and iOS Safari has no
vibration API. Recipes are in [touch-and-input.md](better-pwa/touch-and-input.md).

## Strip the tap flash and text selection only from controls

`-webkit-tap-highlight-color: transparent` removes the loudest "this is a website"
tell and the browser's only tap feedback; set it globally only once every tappable
element has an `:active` state, and never remove focus indicators with it. Suppress
`user-select` (with the `-webkit-` prefix) on control labels, tabs, chips, and drag
handles, and `-webkit-touch-callout` only where the long-press menu fights the
control. Never on `body` or prose: people copy addresses, errors, and order numbers.

## Size the viewport for its job

`100vh` is the viewport with browser chrome collapsed, so on load it overflows and
pins bottom actions under the URL bar. Use `100dvh` for app shells and drawers that
should track the visible area; use `min-height: 100svh` for heroes and first screens
that should stay stable while scrolling. Prefer `min-height` for growing content and
give fixed-height shells a deliberate scroll container.

## Paint edge to edge, pad content to the safe area

Add `viewport-fit=cover` to the single viewport declaration, then pad fixed headers,
tab bars, toasts, and sheets with `env(safe-area-inset-*)` added to the normal
spacing token, with a `0px` fallback. Without the meta tag every inset is `0`.
Backgrounds reach the edges; controls and text clear cutouts and the home indicator.
Check all four edges in landscape and count each inset once, not again in nested chrome.

## Never disable zoom

`user-scalable=no` and a restrictive `maximum-scale` are accessibility failures and
the wrong fix for focus zoom. iOS zooms into fields whose computed font size is under
`16px` and does not zoom back out; fix the size instead, with the two approaches in
[better-typography](better-typography.md). While in the field, set `type`,
`inputmode`, `autocomplete`, and `enterkeyhint` so the right keyboard appears.

## Keep fields reachable with the keyboard open

Neither `dvh` nor safe-area insets guarantee clearance above the software keyboard,
and browsers resize differently. Start with ordinary scrolling and the project's
existing handling; `interactive-widget=resizes-content` helps where supported (check
current support). Add
Visual Viewport logic only for a reproduced obstruction, then verify focus, typing,
dismissal, and reaching the submit action with the keyboard open.

## Give every gesture surface one owner

`touch-action` names what the browser may still do: a JavaScript horizontal drag gets
`pan-y pinch-zoom`, a vertical sheet handle gets `pan-x pinch-zoom`. Put restrictions
on the smallest gesture surface, never `none` on content people scroll past, and
remember a child cannot undo an ancestor's restriction. Prefer native overflow with
CSS scroll snap for ordinary carousels. Use Pointer Events with pointer capture,
handle `pointercancel` without firing the action, and keep a non-drag alternative.
Tracking, thresholds, and release physics are in [gestures](better-animation/gestures.md).

## Contain overscroll where it conflicts

Use `overscroll-behavior-y: contain` on inner scrollers (sheet content, chat lists,
sidebars) so reaching the edge does not move or refresh the page behind; `contain`
keeps the container's own bounce. Suppress root pull-to-refresh only in app-like
shells where it conflicts, and keep it on scrolling documents. Never cancel
`touchmove` to stop scroll chaining, and reuse the project's dialog or sheet
primitive for background scroll locking and focus restoration.

## Match browser chrome to the top surface

Give `theme-color` one value per supported color scheme, matching the header or top
surface rather than the brand color, and keep it in sync with an in-app theme toggle.
Treat it as a hint: some browsers ignore it and tint from the page itself, so the top
surface and `body` background must match too. Declare `color-scheme` only for schemes
the interface supports.

## Give installed mode its own way back

Standalone display removes the URL bar and back button. When installation is in
scope, align the manifest's `theme_color` and `background_color` with the launch
surface, check `apple-mobile-web-app-status-bar-style` separately, and use
`@media (display-mode: standalone)` to add in-app back navigation, share, and
refresh paths the browser chrome used to provide. Never trap the user on a screen.

## Test on a real phone before calling it done

Sticky hover, tap highlight, URL-bar resizing, focus zoom, tap delay, rubber-banding,
safe areas, and the keyboard do not reproduce in desktop emulation. Use the project's
device-preview setup or remote inspection, test an older phone, landscape, keyboard
open, and installed mode when targeted. Without hardware, finish the code-verifiable
work and name the checks that remain unverified. Workflow and checklist are in
[baseline-and-testing.md](better-pwa/baseline-and-testing.md).

## Report

For implementation, report in a few lines: the symptom matched, the one-line cause,
the file and declaration changed, and what still needs a phone. For reviews, use
[better-ui-review](better-ui-review.md)'s evidence, severity, and report format.
