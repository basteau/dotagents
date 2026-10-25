# When sound earns its place

Decision rules for adding, weighting and pruning UI sound. Defaults are silence
and restraint; each row needs a reason to break them.

## Decision matrix

| Moment | Sound? | Why |
| --- | --- | --- |
| Payment, booking or order confirmed | Yes | Significant outcome the user waits for |
| Message sent, upload or export finished | Yes, brief | Reassurance after a deliberate action |
| Error or warning that blocks progress | Yes, soft | Must not be overlooked; always with visible text |
| Notification while the user may look away | Yes | The only case where sound leads, still with a visual |
| Primary brand action on a brochure site (contact) | Maybe, one | A small reward on the most deliberate click |
| Playful brand moment the user triggers on purpose | Maybe, quiet | Only if rare, quieter than the primary sound |
| Toggle, checkbox, minor button | Rarely | Only in sound-forward products, as a tick |
| Inline validation while typing | No | Fires repeatedly; feels like scolding |
| Typing, keystrokes | No | Far too frequent |
| Hover, focus, pointer move | No | Decorative, constant |
| Scroll, drag progress, resize | No | Continuous |
| Keyboard or route navigation | No | Turns browsing into noise |
| Page load, intro, idle animation | No | Unrequested; autoplay |
| Background or empty-space clicks | No | No outcome to confirm |
| Each element in a cascade or field | No | Voices pile up past what anyone can parse |

Ask three questions before adding one. Does the user need to know this outcome?
Could they miss it visually? Will they trigger it rarely enough that it stays
pleasant on the fiftieth time? Two "no" answers mean silence.

## Weight

Weight is the sum of duration, loudness, layers and pitch movement. Match it to
how much the action matters and how long it takes.

| Action class | Character | Audible length | Layers | Starting gain |
| --- | --- | --- | --- | --- |
| Tick: toggle, select, minor tap | Filtered noise click or tiny sine blip | 5–15ms | 1 | 0.05–0.15 sine, about 0.3 filtered noise |
| Pop: small positive, add, like | Sine with a short upward sweep | 40–80ms | 1 | 0.1–0.2 |
| Confirm: send, copy, save | Two notes, rising, the second slightly delayed | ≤200ms | 2 | 0.1–0.15 each |
| Achievement: payment, long task done | Short major chord or arpeggio | ≤800ms | 2–3 | 0.12–0.16 each |
| Error: blocked, failed | Lower, flat or falling, soft attack | 100–300ms | 1–2 | at or below confirm |
| Motion-linked: a flip or slide the sound accompanies | Filtered noise whoosh shaped to the motion | the motion's length | 1 | several dB under confirm |

Gain values are per layer before the master volume; keep the summed layer gain
under about 0.6. Durations are audible length, including release and any reverb
tail. A sound that outlasts its action reads as lag.

## Meaning through pitch

| Direction | Reads as | Use for |
| --- | --- | --- |
| Rising interval (fourth, fifth) | Done, on, yes | Confirm, enable, success |
| Falling interval | Off, closed, dismissed | Disable, close, undo |
| Flat or low, slightly detuned | Attention | Error, warning |
| Same notes, opposite order | A pair | On/off, open/close |

Choose notes from one scale so sounds heard in sequence never clash. Keep a
sound family's timbre consistent: if confirm is a soft sine with a touch of FM,
error is the same instrument played lower, not a different synth.

## Calibration: a restrained brochure site

A landing page for a small studio shipped with exactly two sounds. The contact
button plays a rising two-note chime of about 190ms (a fourth that resolves, so
it reads as "done"). Clicking the wordmark replays its flip animation with a soft
pink-noise whoosh shaped to the flip, about 9dB under the chime. Sound loads
after the page and never at all for reduced-motion visitors, so no toggle is
offered.

What was tried and removed teaches as much:

- A pitched pop on every background click: pleasant once, noise by the tenth.
- A tick per flipping tile: hundreds of events in seconds, over twenty voices
  at once.
- Hover sounds: decorative, no information.
- A three-note success chord on contact: too heavy for sending an email.

Use this as the default bar for marketing and portfolio sites. Products where
sound is part of the identity (games, music tools, chat) may go further, but
they owe the listener a toggle and a volume control.

## Toggle and volume requirements

| Product | Toggle | Volume | Default |
| --- | --- | --- | --- |
| Brochure or portfolio, 1–2 deliberate-click sounds | Optional | No | On, off under reduced motion |
| App with sound on several actions | Required, persisted | Recommended | On at subtle volume, off under reduced motion |
| Frequent or ambient sound (chat, game, editor) | Required, reachable from the main UI | Required | Ask or start subtle |
| Workplace or shared-space tools | Required | Required | Consider off by default |

Persist the preference, honor it immediately, and never play the toggle's own
confirmation when the user just turned sound off.
