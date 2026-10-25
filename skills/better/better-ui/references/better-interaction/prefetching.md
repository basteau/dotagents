# Prefetching on intent

Prefetching hides latency by starting work before the click. It pays off only where
navigation or data waits are noticeable, and it costs bandwidth, server load, and
battery when it guesses wrong.

## Decide whether it belongs

| Worth it | Not worth it |
| --- | --- |
| Data-heavy dashboards, detail pages behind slow APIs | Static sites that already navigate instantly |
| Multi-page apps with a measurable wait on common paths | Apps whose data is already loaded client-side |
| Product and article pages reached from lists | Destinations rarely visited, or personalized and expensive to build |

Measure first: which part of the wait dominates (DNS and connection, server time,
JavaScript bundle, data, render). Prefetching data does not help a slow render, and
preconnecting does not help a slow API. Use the router's or framework's prefetch and
cache facilities before adding a library.

## Pick signals by how early and how certain they are

| Signal | Lead time | Certainty | Use for |
| --- | --- | --- | --- |
| Pointer trajectory toward the target | Highest; fires while the cursor is still moving | Medium | Heavier destinations on pointer devices |
| Hover intent (pointer rests ~50-100ms) | Medium | Medium-high | The default for links and cards on desktop |
| `pointerdown` / `touchstart` | Roughly 80-150ms before `click` | Very high | Every device; the safe floor, and the touch strategy |
| Keyboard focus, or focus a few tab stops away | Medium | Medium-high | Keyboard users, who never hover |
| In viewport | Earliest | Low | Only cheap resources on short lists, or when bandwidth is plentiful |

Combine signals per device: trajectory or hover intent with pointer-down on fine
pointers, touch-start plus focus elsewhere. Never assume a cursor; touch and keyboard
must each have a path. Expand the prediction area beyond the visible target (around
20px) so a trajectory starts loading sooner; this is separate from the hit area.

## Guardrails

- Speculate only with safe, idempotent reads. Never prefetch a URL whose GET has side
  effects (logout, mark-as-read, add to cart) or counts as a page view.
- Deduplicate in-flight requests and respect cache freshness; a prefetched response
  that is stale on arrival is wasted.
- Cap concurrency and prefer low priority (`fetch(url, { priority: "low" })`, idle
  callbacks) so speculation never delays the current page.
- Back off on constrained networks: skip or downgrade when
  `navigator.connection?.saveData` is true or `effectiveType` reports a slow
  connection. These properties are not available in every browser; absence means
  unknown, not fast.
- Prerendered pages run their scripts. Defer analytics and other side effects until
  the page is actually shown (`document.prerendering`, `prerenderingchange`).
- Do not prefetch everything visible in the viewport just because the framework can.

## Verify

Compare time from click to usable destination with and without prefetch on a
throttled network and on a touch device. Check the network panel for wasted or
duplicate requests while moving the pointer across a list. Remove the prediction if
the gain is not noticeable.

## Platform: Speculation Rules

For multi-page sites in Chromium-based browsers, the Speculation Rules API prefetches
or prerenders from declared rules; other browsers ignore it, so it is a safe
progressive enhancement:

```html
<script type="speculationrules">
{
  "prefetch": [{
    "where": { "and": [{ "href_matches": "/*" }, { "not": { "href_matches": "/logout" } }] },
    "eagerness": "moderate"
  }]
}
</script>
```

`conservative` speculates on pointer or touch down; `moderate` on hover intent;
`eager` and `immediate` earlier and more often. Exact heuristics vary by browser
version and device. Exclude any URL with side effects.

## Optional: hand-rolled intent prefetch

When the router exposes a `prefetch(href)` call but no intent strategy:

```js
function prefetchOnIntent(link, prefetch, { hoverDelay = 80 } = {}) {
  let timer;
  let done = false;
  const go = () => {
    if (done || navigator.connection?.saveData) return;
    done = true;
    prefetch(link.href);
  };
  link.addEventListener("pointerenter", (e) => {
    if (e.pointerType === "mouse") timer = setTimeout(go, hoverDelay);
  });
  link.addEventListener("pointerleave", () => clearTimeout(timer));
  link.addEventListener("pointerdown", go);
  link.addEventListener("focus", go);
}
```

`pointerdown` fires for touch as well, so the same listener covers touch devices.
Trajectory prediction needs pointer-velocity tracking; use a maintained library for
it rather than writing one, and confirm it falls back to touch and keyboard signals.

## Optional: Next.js

`<Link>` prefetches routes as they enter the viewport by default in production. For
long lists, set `prefetch={false}` and call `router.prefetch(href)` from an intent
signal instead. Check the installed version's documentation; defaults have changed
between releases.
