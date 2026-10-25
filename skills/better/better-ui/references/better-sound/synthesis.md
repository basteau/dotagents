# Synthesis

Procedural UI sounds with the Web Audio API: building blocks, envelopes, recipes,
parameter ranges and how to tune by description. Recipes take `(c, out)`, a
context and the master gain from [playback.md](playback.md), and schedule at
`c.currentTime`. When the project uses a declarative sound-definition library,
apply the same rules to its definitions instead of hand-building nodes.

## Pick the source by the sound

| Sound | Source | Shaped by |
| --- | --- | --- |
| Click, tap, tick, key | Noise burst | Bandpass filter, very fast decay |
| Pop, blip, confirm, chime | Oscillator with pitch movement | Envelope, optional second note |
| Whoosh, swipe, flip | Noise, longer | Filter frequency sweep and a slow envelope |
| Error, warning | Oscillator, lower register | Falling or flat pitch, soft attack |

An oscillator click sounds like a beep, and a static-pitch tone sounds like a
test signal. Movement is what makes a synthesized sound feel physical.

| Waveform | Character | Use |
| --- | --- | --- |
| Sine | Pure, soft | Default for tonal UI sounds |
| Triangle | Soft with a little body | Softer errors, warmer pops |
| Square, sawtooth | Buzzy, aggressive | Only filtered and quiet, for retro or game styles |

## Envelope rules

- Set the start value with `setValueAtTime` before any ramp; a ramp with no
  anchor jumps or glitches.
- Decay with `exponentialRampToValueAtTime`; linear fades sound unnatural and end
  in an audible step.
- Exponential ramps cannot start from or reach 0. Use `0.0001` to start and
  `0.001` as the floor, then stop the source.
- Give tones a 1 to 3ms attack to avoid an onset click; noise clicks may start at
  full level.
- Stop sources just after the envelope ends, never before it.

```js
function envelope(c, peak, attack, decay, t) {
  const g = c.createGain();
  g.gain.setValueAtTime(0.0001, t);
  g.gain.linearRampToValueAtTime(peak, t + attack);
  g.gain.exponentialRampToValueAtTime(0.001, t + attack + decay);
  return g;
}

const noiseBuffers = new WeakMap(); // one second of white noise per context
function noiseBuffer(c) {
  if (!noiseBuffers.has(c)) {
    const b = c.createBuffer(1, c.sampleRate, c.sampleRate);
    const d = b.getChannelData(0);
    for (let i = 0; i < d.length; i++) d[i] = Math.random() * 2 - 1;
    noiseBuffers.set(c, b);
  }
  return noiseBuffers.get(c);
}

function tone(c, out, { type = "sine", from, to = from, gain = 0.12,
                        attack = 0.002, decay = 0.08, delay = 0 }) {
  const t = c.currentTime + delay;
  const osc = c.createOscillator();
  osc.type = type;
  osc.frequency.setValueAtTime(from, t);
  if (to !== from) osc.frequency.exponentialRampToValueAtTime(to, t + attack + decay / 2);
  const env = envelope(c, gain, attack, decay, t);
  osc.connect(env).connect(out);
  osc.start(t);
  osc.stop(t + attack + decay + 0.02);
  osc.onended = () => { osc.disconnect(); env.disconnect(); };
}
```

## Recipes

```js
// Tick: about 8ms of bandpassed noise. Random offset so repeats are never identical.
export const click = (c, out) => {
  const t = c.currentTime;
  const src = c.createBufferSource();
  src.buffer = noiseBuffer(c);
  const filter = c.createBiquadFilter();
  filter.type = "bandpass";
  filter.frequency.value = 4000;
  filter.Q.value = 3;
  const env = envelope(c, 0.3, 0.0005, 0.008, t);
  src.connect(filter).connect(env).connect(out);
  src.start(t, Math.random() * 0.5);
  src.stop(t + 0.02);
  src.onended = () => [src, filter, env].forEach((n) => n.disconnect());
};

// Pop: a quick downward sweep, like a bubble.
export const pop = (c, out) => tone(c, out, { from: 900, to: 600, gain: 0.15, decay: 0.05 });

// Confirm (send, copy, save): two notes rising a fourth, second one delayed.
export const confirm = (c, out) => {
  tone(c, out, { from: 784, gain: 0.13, decay: 0.06 });                 // G5
  tone(c, out, { from: 1046, to: 1100, gain: 0.11, decay: 0.09, delay: 0.045 }); // C6, lifts slightly
};

// Error: soft, lower, falling. Informs without scolding.
export const error = (c, out) => {
  tone(c, out, { type: "triangle", from: 440, gain: 0.12, attack: 0.005, decay: 0.09 });
  tone(c, out, { type: "triangle", from: 330, gain: 0.1, attack: 0.005, decay: 0.14, delay: 0.09 });
};

// Whoosh shaped to a motion of `ms`: filter opens into the midpoint, closes as it lands.
export const whoosh = (ms = 600, gain = 0.15) => (c, out) => {
  const t = c.currentTime, half = ms / 2000, end = t + ms / 1000;
  const src = c.createBufferSource();
  src.buffer = noiseBuffer(c);
  const filter = c.createBiquadFilter();
  filter.type = "bandpass";
  filter.Q.value = 1.2;
  filter.frequency.setValueAtTime(500, t);
  filter.frequency.exponentialRampToValueAtTime(2400, t + half);
  filter.frequency.exponentialRampToValueAtTime(600, end);
  const env = envelope(c, gain, half, half, t);
  src.connect(filter).connect(env).connect(out);
  src.start(t);
  src.stop(end + 0.02);
  src.onended = () => [src, filter, env].forEach((n) => n.disconnect());
};
```

For a pair such as toggle on and off, reuse the same two notes and reverse their
order. For variety across rapid repeats, jitter pitch by 10 to 20 cents
(`osc.detune.value`) and gain by a few percent.

## Parameter ranges

| Parameter | Starting range | Notes |
| --- | --- | --- |
| Click length | 5–15ms | Over 30ms it stops being a click |
| Click bandpass frequency | 3000–6000Hz | Below about 1500Hz sounds muffled |
| Bandpass Q, clicks | 2–5 | Above about 10 rings harshly |
| Bandpass Q, whoosh | 0.7–1.5 | Broad and airy |
| Tone fundamental | 400–2000Hz | Laptop and phone speakers barely play below 200Hz; pure sines above 4kHz pierce |
| Pitch sweep | 1.2–1.5× over 30–60ms | Up for confirm and enable, down for bubble pops and dismissals |
| Gap between notes | 30–70ms | Shorter blurs, longer sounds like two events |
| Tone attack | 1–3ms | 5ms or more for soft errors |
| Gain per layer | 0.05–0.3 | Never above 1; summed layers under about 0.6 |
| Reverb or room | Mix ≤ 0.1, decay ≤ 0.3s | Adds polish; more turns a tick into a cave |

Levels are relative: −3dB is ×0.71 and −6dB is ×0.5. Set secondary sounds a
few dB under the primary one rather than guessing raw gains.

## Tuning by description

| Feedback | Change |
| --- | --- |
| Too harsh, shrill | Lower filter frequency, lower Q, sine or triangle |
| Muffled, dull | Raise filter frequency |
| Too long, laggy | Shorter decay, smaller reverb mix |
| Cuts off abruptly | Exponential decay, stop after the envelope ends |
| Clicks at start or end | Set the start value, add 1–3ms attack, ramp to 0.001 |
| Beepy, cheap | Add pitch movement, a second note or a touch of FM |
| Robotic on repeat | Jitter detune and gain slightly per play |
| More mechanical, crisper | Higher Q, faster decay |
| Softer | Lower gain, triangle or sine, longer attack |
| Thin, tinny | Lower the fundamental a little or add a quiet octave below |
| Louder than its neighbors | Drop in 3dB steps until it sits under the primary sound |

## Measure before shipping

Render each sound offline and check it against the weight table in
[when-to-use.md](when-to-use.md#weight). Peak should sit well below 0dBFS before
the master volume is applied.

```js
async function measure(sound, seconds = 1) {
  const sr = 48000, off = new OfflineAudioContext(1, sr * seconds, sr);
  sound(off, off.destination);
  const x = (await off.startRendering()).getChannelData(0);
  let peak = 0, last = 0;
  for (let i = 0; i < x.length; i++) {
    const a = Math.abs(x[i]);
    if (a > peak) peak = a;
    if (a > 0.001) last = i;
  }
  return { audibleMs: (last / sr) * 1000, peakDb: 20 * Math.log10(peak) };
}
```

For a whole flow, log every play with a timestamp, then count events, maximum
overlapping voices and how much of the session is audible. Numbers catch what a
single listen misses; ears catch what numbers miss. Use both.
