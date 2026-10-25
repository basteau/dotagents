# Interaction and feedback

Use this when designing choices, forms, asynchronous states, flow completion, or
perceived speed: when feedback appears and how a flow recovers. The aim is an
interface that answers every input at once, asks for as little as the task allows,
and ends each flow somewhere clear. Thresholds below are starting points; keep the
project's components, data layer, and tokens.

Control semantics, focus, hit-area sizes, and announcements belong to
[accessibility](better-accessibility.md). The words in errors, empty states, and
confirmations belong to [writing](better-writing.md). Spacing-based grouping belongs
to [layout](better-layout.md); motion budgets and press-scale values to
[animation](better-animation.md); tap activation on touch to [PWA](better-pwa.md);
audio feedback to [sound](better-sound.md). Each UX law is translated into a build
rule in [laws-of-ux.md](better-interaction/laws-of-ux.md).

## Lead with the decision in front of the user

Show the choices this step needs and give the likely next action the strongest
emphasis. Put the frequent few up front and move the rest behind a labeled
disclosure or overflow menu, but keep expert surfaces dense when comparison needs
everything visible. Judge by task frequency and comprehension, not a fixed count.
Cut anything that does not help the decision: repeated warnings, redundant labels,
decorative icons. See [laws-of-ux.md](better-interaction/laws-of-ux.md#decisions-and-memory).

## Keep familiar patterns familiar

Use the conventions people already know from other products and the platform:
links that look like links, search where search usually is, Escape and back that
undo navigation. Controls that do the same thing look and behave the same; controls
that do different things look different. Invent a new pattern only when it is
clearly better, and teach it in place.

## Put targets where the hand already is

Target cost grows with distance and shrinks with size. Place the next action near
the content or pointer that triggered it, keep frequent actions large and close,
and keep destructive ones away from them. Let edge-anchored controls run to the
edge so an overshoot still hits. Sizes and pseudo-element extensions live in
[hit-areas.md](better-accessibility/hit-areas.md).

## Show, don't make people remember

Keep earlier answers, selected items, and totals visible on the step that uses
them. Chunk long values for reading (`4532 0151 1283 0366`) while storing them
raw, and break long forms into labeled groups. Show current position in multi-step
work and let people revisit earlier steps without losing input.

## Let the system absorb the complexity

Accept harmless variation: whitespace, separators in phone and card numbers, a
pasted one-time code, mixed case in email. Normalize for storage and display, keep
validation faithful to the data contract, and pick safe defaults. Ambiguous dates
and consequential values need explicit interpretation, never a silent guess. On
error, preserve the input and put recovery beside the affected control.

## Acknowledge every input immediately

Show press, selection, or pending state within the same frame, well under 100ms,
and before the network answers. Keep the control's label recognizable while work
is pending and block duplicate consequential submissions. Aim to finish or show
meaningful progress within about 400ms. Budgets and recipes are in
[feedback-and-latency.md](better-interaction/feedback-and-latency.md).

## Match the loading state to what you know

Keep existing content on screen during refresh. Use a skeleton when the arriving
structure is known, a delayed indicator when the wait is short and unknown, and
determinate progress when the wait is long. Reserve space so arriving content does
not shift reading position or move a target under the pointer. A blank screen is
never the loading state. See the [decision table](better-interaction/feedback-and-latency.md#loading-states).

## Update optimistically only when you can roll back

Optimistic updates suit frequent, reversible, predictable operations: toggles,
reordering, likes, renames. Each needs a pending marker, a defined rollback, and an
inline error with retry. Payments, irreversible deletes, permissions, and anything
others see immediately wait for confirmation. Progress must be honest; decorative
timing cannot establish success. Recipe: [optimistic updates](better-interaction/feedback-and-latency.md#optimistic-updates).

## Prefer undo to confirmation

For reversible actions, act at once and offer undo where the result appears. Delay
the real commit or soft-delete until the undo window closes, and keep undo
reachable by keyboard. Reserve confirmation dialogs for irreversible, shared, or
bulk destruction; their wording is in [writing](better-writing.md).

## Survive slow, repeated, and out-of-order responses

Verify success, failure, retry, double activation, offline, and a slow network.
Cancel or ignore superseded requests so a late response cannot overwrite newer
input. Make retries safe for the server: idempotent requests or an idempotency key.
Recipes are in [feedback-and-latency.md](better-interaction/feedback-and-latency.md#races-and-retries).

## Show progress and what remains

In multi-step work, show where the person is, how much is left, and what is
already done; progress never jumps backward or invents steps. Surface an
unfinished state, such as a half-done setup, only when finishing benefits the
user, and make the reminder dismissible.

## Finish where the action happened

People remember the peak and the end. Make success visible in place: the saved
state, created item, receipt, or next useful step, instead of an abrupt redirect.
Match prominence to consequence; a routine save needs a quiet status, a finished
onboarding or purchase earns a clear moment. Make error recovery as considered as
success, since a failure is often the peak people remember.

## Keep transient feedback readable

Toasts pause their timers on hover, focus, and while the tab is hidden; those with
an action or an error stay until dismissed. Tooltips delay the first open, then open
instantly across neighbors. Recovery never lives only in something that vanishes.

## Never let motion or sound carry feedback alone

Every animated state change also needs a static cue: color, icon, label, or
position. People can act as soon as the interface is ready, mid-animation
included, and repeated actions are never slowed by animation. Sound only
supplements visible feedback; see [sound](better-sound.md).

## Prefetch on intent, after measuring

Find where the wait occurs first: network, server, bundle, or render. Reuse the
project's router and cache. Prefetch likely destinations from intent signals such
as pointer trajectory, hover, pointer down, and keyboard focus, with a touch path
that needs no cursor, and never everything in the viewport. Keep speculative work
read-only, bounded, and respectful of data-saver settings. Strategy and guardrails
are in [prefetching.md](better-interaction/prefetching.md).

## Before you finish

| Mistake | Fix |
| --- | --- |
| Blank region or `return null` while loading | Skeleton, retained content, or a delayed indicator |
| Spinner flashes for a 50ms response | Delay the indicator; once shown, hold it briefly |
| Submit can fire twice | Mark pending on the first activation; make the request idempotent |
| Late response overwrites newer input | Abort or ignore superseded requests |
| Optimistic change silently reverts on failure | Roll back visibly with an inline error and retry |
| Confirmation dialog for a reversible action | Act at once and offer undo |
| Redirect to home right after submit | Show success in place with the next useful step |
| Every visible link prefetched | Prefetch on intent; respect data saver |
| Toast with an error or action that auto-dismisses | Keep it until dismissed |

## Reporting

Without a browser, trace each async path in code: pending, success, failure, retry,
cancellation, and rollback. With one, throttle the network, double-click
consequential actions, submit invalid and pasted input, and check what remains on
screen at each step. Report each check you could not run as `Not verified`. For
reviews, use [the shared review format](better-ui-review.md) and report observed
failures with their effect on the task.
