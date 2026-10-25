# Playback

Engine structure, preferences, preloading, latency and cleanup for UI sound on
the web. Reuse the project's sound library or wrapper when it has one; these
patterns describe what it should already do.

## One engine, one context

Browsers limit how many `AudioContext`s a page can hold, and each costs a
hardware stream. Create one lazily, route every sound through one master gain,
and keep the user's preference in front of everything.

```js
let ctx = null, master = null;
const prefs = { enabled: true, volume: 0.3 }; // load from the project's preference store

const quiet = () =>
  !prefs.enabled || matchMedia("(prefers-reduced-motion: reduce)").matches;

function context() {
  if (!ctx || ctx.state === "closed") {
    ctx = new AudioContext({ latencyHint: "interactive" });
    master = ctx.createGain();
    master.gain.value = prefs.volume;
    master.connect(ctx.destination);
  }
  return ctx;
}

export function setVolume(v) {
  prefs.volume = v;
  if (master) master.gain.setTargetAtTime(v, ctx.currentTime, 0.01); // no zipper clicks
}
```

Create or resume the context inside the gesture handler itself, synchronously.
Doing it after an `await` or in a timer loses the user activation, and Safari
keeps the context suspended.

## Resume, and drop late sounds

A context starts `suspended` until a gesture, and can suspend again after the tab
is backgrounded or the device sleeps. Check before every play. If waking takes
too long, skip the sound: feedback that arrives after the moment reads as a bug.

```js
export function play(sound) {
  if (quiet()) return;
  const c = context(), asked = performance.now();
  if (c.state === "running") return sound(c, master);
  c.resume().then(
    () => performance.now() - asked < 150 && sound(c, master),
    () => {} // stay silent on failure; sound is never critical
  );
}
```

`sound(c, out)` is any function that schedules nodes into `out` at
`c.currentTime`, such as the recipes in [synthesis.md](synthesis.md).

## Load after the page, not on first click

Fetch the sound module or sample files after `load`, or on idle, so the first
click finds them ready and the page's critical path stays clean. Skip loading
entirely when the preference or reduced motion means the sounds will never play.

```js
if (!quiet()) addEventListener("load", () => setTimeout(loadSounds, 300));
```

## Samples: decode once, play many

For recorded sounds, decode each file into an `AudioBuffer` once and start a new
`AudioBufferSourceNode` per play. Source nodes are single-use and cheap; this
replays instantly, overlaps cleanly and avoids `HTMLAudioElement` start lag.

```js
const buffers = new Map();

async function loadSample(url) {
  if (!buffers.has(url)) {
    const data = await (await fetch(url)).arrayBuffer();
    buffers.set(url, await context().decodeAudioData(data));
  }
  return buffers.get(url);
}

const sample = (url) => (c, out) => {
  const buffer = buffers.get(url);
  if (!buffer) return; // not ready: skip rather than play late
  const src = c.createBufferSource();
  src.buffer = buffer;
  src.connect(out);
  src.onended = () => src.disconnect();
  src.start();
};
```

Decoding needs a context; creating one before a gesture is allowed, it just
stays suspended. Keep files short, trimmed of leading silence, mono, and in a
format every target browser decodes (AAC/MP4 or MP3; add Opus where supported).

When a project already uses `HTMLAudioElement`, preload it (`preload="auto"`,
`.load()` on init), set `currentTime = 0` before each `play()` so rapid triggers
restart instead of being ignored, and catch the promise `play()` returns.

## Clean up every node

Disconnect sources, gains and filters in the source's `onended`. Stop
oscillators explicitly at the end of their envelope; an oscillator without
`stop()` runs forever inside the graph. Effects with their own oscillators
(chorus, tremolo) need their own stop on dispose.

## Cap voices and rate

Guard against the bursts that make sound unbearable. Ignore repeats of the same
sound within its own length, cap simultaneous voices to a handful, and never tie
a sound to an event that can fire per frame or per element.

```js
const lastPlayed = new Map();
function throttled(name, sound, minGapMs = 80) {
  const now = performance.now();
  if (now - (lastPlayed.get(name) ?? 0) < minGapMs) return;
  lastPlayed.set(name, now);
  play(sound);
}
```

## Share the device politely

Where the Audio Session API exists, mark UI sound as ambient so it mixes with
the listener's music instead of pausing it and respects the iOS silent switch.
Feature-detect it and ignore failures.

```js
try { if (navigator.audioSession) navigator.audioSession.type = "ambient"; } catch {}
```

Never start sound on page load, on `visibilitychange`, or from a timer the user
did not start.

## Preferences

- Persist the toggle and volume in the project's settings store; read them before
  the first play, not after.
- Apply changes immediately through the master gain; do not rebuild sounds.
- A preview when the user adjusts volume is fine. Playing a sound when they turn
  sound off is not.
- Reduced motion silences sound by default, but an explicit "sound on" choice
  from the user overrides it.

## Optional: React

Keep the engine in a plain module and expose it through a small hook or context,
so components call `play(sounds.sent)` without owning audio state.

```tsx
const SoundContext = createContext({ play: (_s: Sound) => {} });

export function SoundProvider({ enabled, volume, children }) {
  useEffect(() => { prefs.enabled = enabled; setVolume(volume); }, [enabled, volume]);
  return <SoundContext.Provider value={{ play }}>{children}</SoundContext.Provider>;
}

export const useSound = () => useContext(SoundContext).play;
```

Call `play` in the event handler, next to the visible state change, not in an
effect that reacts to that state; effects fire on re-renders and restored state.
