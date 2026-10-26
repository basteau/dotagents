# Enter, exit, and presence

How things arrive and leave. The principles hold on any platform; the web and
Motion sections are optional implementations. Component-specific values are in
[patterns.md](patterns.md), exact CSS in [recipes.md](recipes.md).

## Exit needs a lifecycle, not just a style

Removing an element from the tree removes it at once; an exit only plays if
something keeps it presented until the exit finishes. Use the component's or
platform's presence support (a transition group, a dialog's closing state, discrete
`display` transitions, a presence wrapper) rather than delaying state with timers.
Entry animation alone never produces an exit.

While an element is leaving, it is semantically gone: disable its controls, keep it
out of focus order and hit testing, and move focus as the semantic state dictates.
If removal depends on async work (saving, measuring, cleanup), signal completion
explicitly; never leave a ghost element mounted because the done signal was skipped.

## Mirror the entry path

If something disappears one way, people expect it to have come from there. Exit
along the path it entered: a toast from the bottom leaves through the bottom; a
panel in from the right leaves to the right. Mismatched paths (in from the right,
out through the bottom) read as two unrelated events.

Exits are quieter than entries: attention has already moved on.

| Exit | Use when | Starting point |
| --- | --- | --- |
| Subtle | Most dismissals; direction matters but distance does not | Opacity plus a small fixed offset (about 12px), optional 4px blur, ~150ms ease-out |
| Full | The destination carries meaning: a drawer closing, a card returning to a list | Slide the full distance along the entry path, ~200ms |
| Instant | Frequent or repeated actions, reduced motion, or motion that says nothing | Remove at once |

Exit shorter than the matching entry (for example 150ms against 300ms). Never exit
dramatically: full-height travel plus heavy scale on a routine dismissal steals
attention from the next thing.

## Give each item a stable identity

Presence tracking needs a stable key per item (an id, not a list index). An index
key makes the wrong element exit when the list changes. When swapping one thing for
another in place (an icon, a label, a step), key it by state so the old and new
versions are distinct elements.

## Choose how old and new overlap

| Mode | Behavior | Use for | Watch |
| --- | --- | --- | --- |
| Simultaneous | Old exits while new enters, both in flow | Crossfades where both can share a position | Both occupy space; take the exiting one out of flow |
| Sequential | New waits until old finishes | Steps where both must never be seen together | Nearly doubles total time; halve each half |
| Pop out of layout | Exiting element leaves flow at once; siblings reflow while it fades | Lists, icon swaps, removals with layout animation | Needs a positioned parent so the popped element stays in place |

Default to pop or simultaneous. Pick sequential only when overlap would confuse, and
budget the combined duration against the usual limits.

## Coordinate nested exits

When a parent leaves, its animated children should leave with it, not vanish. Either
propagate the exit to children or let the parent's own exit cover them. Keep child
exits no longer than the parent's, so the parent never waits on a long tail or cuts
a child mid-motion.

## Skip state animations on first render

Content already in its default state on load should not animate in; only later state
changes animate. Disable initial presence animation for icon swaps, toggles, tabs,
and segmented controls. Never disable it around an intentional first-load entrance
such as a staged hero, or the entrance disappears. Check the component on a full
page refresh.

## Keep the settled state robust

Put the hidden state in the animation's starting point, not in the base style, so
content stays visible if the animation never runs (script failure, reduced motion,
disabled animations). Completion logic must not depend only on an end event that a
skipped animation never emits. Under reduced motion, keep a brief opacity change if
it helps and drop translation, scale, and blur; see
[better-accessibility](../better-accessibility.md).

## Web: entering from display none

An element shown from `display: none` (a popover, a dialog) has no previous style to
transition from. `@starting-style` supplies the starting values, and
`transition-behavior: allow-discrete` on `display` (and `overlay` for top-layer
elements) keeps it rendered through the exit. The open state's duration applies on
enter, the closed state's on exit. Browsers without support show and hide instantly,
which is an acceptable fallback. Code:
[recipes.md](recipes.md#entering-from-display-none-native-dialog-and-popover).

## Optional: Motion `AnimatePresence`

Use these only in a project that already uses `motion` (or `framer-motion`; match
the existing import path).

- **Wrap conditionals.** A conditionally rendered `motion.*` element only exits
  inside `<AnimatePresence>`, and it needs an `exit` prop; usually mirror `initial`.
- **Key lists by id**, and key in-place swaps by state.
- **Modes.** `mode="popLayout"` for lists and icon swaps; `mode="wait"` doubles the
  time, so halve durations; the default `sync` lets old and new compete for space.
- **First render.** `initial={false}` on the wrapper skips mount animations for
  state swaps; never around a first-load entrance.
- **Nested exits.** Add `propagate` to nested `AnimatePresence` so children animate
  out with their parent instead of disappearing.
- **Presence hooks** run in the exiting child, not the parent. Use `useIsPresent()`
  to disable interaction while exiting; with `usePresence()`, always call
  `safeToRemove` once async cleanup finishes.

```tsx
<AnimatePresence initial={false} mode="popLayout">
  {items.map((item) => (
    <motion.li
      key={item.id}
      layout
      initial={{ opacity: 0, y: 12 }}
      animate={{ opacity: 1, y: 0 }}
      exit={{ opacity: 0, y: 12, transition: { duration: 0.15 } }}
    >
      <Row item={item} />
    </motion.li>
  ))}
</AnimatePresence>

function Row({ item }) {
  const isPresent = useIsPresent();
  return <button disabled={!isPresent}>{item.label}</button>;
}
```

Under reduced motion, `<MotionConfig reducedMotion="user">` turns off transform and
layout animation but keeps opacity; branch on `useReducedMotion()` where a recipe also
animates `filter`.
