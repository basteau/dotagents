# Feedback and latency

Response budgets, loading states, optimistic updates, undo, races, and transient
feedback. Values are starting points; use the project's data layer and components.
Motion values for press and pending states live in [better-animation](../better-animation.md).

## Response budgets

| Elapsed since input | What the person needs |
| --- | --- |
| Same frame, under 100ms | Press, selection, or focus state; the control visibly heard them |
| Up to about 400ms | The result, or a pending state that keeps the label recognizable |
| About 400ms to 1s | Retained content plus a quiet indicator; no layout jump |
| 1s to about 10s | A skeleton or indicator tied to the region that is loading |
| Longer than about 10s | Determinate progress or step text, and freedom to leave and come back |

A fast interaction never waits for its own feedback animation. Keep long work
cancellable where the operation allows it.

## Loading states

| Situation | Use |
| --- | --- |
| Refreshing data already on screen | Keep the content, mark it updating inline, swap when ready |
| First load, structure known | Skeleton matching the final layout's size and rhythm |
| First load, structure unknown, short | Indicator shown only after a delay |
| Long or multi-stage work | Determinate progress, or named stages when percent is unknown |
| Background work not blocking the task | A status line or polite announcement; the page stays usable |
| Navigation | Respond on the trigger (pending link state), then load the destination shell first |

Reserve the final size for images, embeds, and lists so arrival does not shift text
or move a target under the pointer. Skeletons that do not resemble the final layout
read as a second layout change.

### Delay, then hold

Showing a spinner for a 60ms response is a flash, not feedback. Delay the indicator,
and once it appears keep it long enough to read so it does not flicker:

```js
function withIndicator(promise, show, hide, { delay = 400, minimum = 500 } = {}) {
  let shownAt = 0;
  const timer = setTimeout(() => { shownAt = performance.now(); show(); }, delay);
  return promise.finally(async () => {
    clearTimeout(timer);
    if (!shownAt) return;
    const remaining = minimum - (performance.now() - shownAt);
    if (remaining > 0) await new Promise((r) => setTimeout(r, remaining));
    hide();
  });
}
```

The pressed or pending state on the trigger still appears immediately; only the
larger indicator waits. A brisk indicator reads as faster than a slow one for the
same wait.

## Optimistic updates

Suitable when the operation is frequent, reversible, and almost always succeeds:
toggles, reordering, renames, reactions, adding to a list. Not suitable for
payments, irreversible deletes, permission changes, or anything that others see
or rely on before the server agrees.

1. Record the previous state.
2. Apply the change locally and mark it pending (a muted style, a small indicator).
3. Send the request with a stable id so a retry cannot duplicate it.
4. On success, replace the local guess with the server's version.
5. On failure, restore the previous state where the person is looking, explain
   what failed, and offer retry. Never revert silently.

```js
async function toggleStar(item) {
  const previous = item.starred;
  setStar(item.id, !previous, { pending: true });
  try {
    const saved = await api.setStar(item.id, !previous);
    setStar(item.id, saved.starred, { pending: false });
  } catch {
    setStar(item.id, previous, { pending: false });
    showInlineError(item.id, "Couldn't save the star.", { retry: () => toggleStar(item) });
  }
}
```

Rapid repeated toggles also need the latest-wins guard from
[races and retries](#races-and-retries).

### Optional example: React

React's `useOptimistic` shows the guess while an action is pending and falls back
to the real state when it settles. A thrown error reverts the list, so still
surface the failure:

```tsx
const [shown, addOptimistic] = useOptimistic(todos, (state, todo) => [
  ...state,
  { ...todo, pending: true },
]);

async function create(formData: FormData) {
  const todo = { id: crypto.randomUUID(), title: String(formData.get("title")) };
  addOptimistic(todo);
  try {
    await createTodo(todo);
  } catch {
    setError("Couldn't add the task. Try again.");
  }
}
// <form action={create}>…</form>
```

## Undo

Remove or change the item in view at once and show undo beside the result. Prefer a
server-side soft delete with a restore call; a client-side delayed commit is lost if
the tab closes before it fires. Keep the undo control reachable by keyboard and long
enough to read; pausing its timer on hover and focus follows the toast rules below.

For a destructive action that is frequent and deliberate, a hold-to-confirm control
works: the press fills slowly and linearly (around 1-2s), release snaps back fast.
Slow where the person decides, fast where the system responds.

## Races and retries

Cancel the superseded request when the transport allows it:

```js
let controller;
async function search(query) {
  controller?.abort();
  controller = new AbortController();
  try {
    const res = await fetch(`/api/search?q=${encodeURIComponent(query)}`, {
      signal: controller.signal,
    });
    render(await res.json());
  } catch (error) {
    if (error.name !== "AbortError") showError(error);
  }
}
```

When the request cannot be aborted, apply only the latest response:

```js
let latest = 0;
async function save(value) {
  const id = ++latest;
  const result = await api.save(value);
  if (id === latest) applyServerState(result);
}
```

Mark a consequential submit pending on its first activation and ignore further
activations until it settles; the server still needs an idempotency key, because
double requests also come from retries and flaky networks. Keep entered data after
a failed submission and put the retry beside the error.

## Transient feedback

- Toasts: one region, newest in a consistent place. Pause the dismiss timer on hover,
  on focus within, and while the page is hidden (`document.hidden` on the web). Lengthen the timer for longer
  text. Toasts with an action or an error persist until dismissed. Collapse repeats
  of the same message instead of stacking them.
- Tooltips: a short open delay (around 300-500ms) prevents accidental triggers. Once
  one is open, neighbors open instantly with no transition until the pointer leaves
  the group. Tooltips never hold the only copy of essential information, and touch
  has no hover, so the same content needs another path.
- Inline status: prefer a quiet "Saved" beside the edited field to a toast for
  routine saves; reserve toasts for results away from the trigger.

Announcement mechanics (`role="status"`, live regions) live in
[better-accessibility](../better-accessibility.md).

## Success and completion

Show the outcome where the action happened: the new item in the list, the updated
value, the receipt. Offer the next useful step rather than redirecting to a home
screen. Scale the moment to the consequence; a first successful setup or purchase can
earn a distinct state, a routine save cannot. Check the success state with real
content and on the slow path, not only the fast one.
