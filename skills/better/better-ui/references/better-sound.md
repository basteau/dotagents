# Better sound

Sound is the loudest feedback an interface has. It reaches people who are not
looking, it cannot be scrolled past, and it follows them out of the tab into the
room. Use it to confirm the few moments that matter and leave everything else
silent. The default answer to "should this make a sound?" is no.

Start from the project: its sound library or asset folder, preference store,
reduced-motion handling and existing sound family. Keep them. Values below are
starting points to tune by ear on real speakers and earbuds.

Visible feedback, pending states and completion belong to
[better-interaction](better-interaction.md). Motion that a sound accompanies
belongs to [better-animation](better-animation.md). Captions, transcripts and
media controls belong to [better-accessibility](better-accessibility.md).

## Earn the sound before designing it

Sound fits rare, deliberate moments whose outcome the user wants to know: a
message sent, a payment confirmed, an upload finished, an error that must not be
missed, a notification that arrives while they look elsewhere. Even then, one
sound per product moment, not per control. The decision matrix is in
[when-to-use.md](better-sound/when-to-use.md).

## Never sound the frequent or the decorative

No sound on hover, scroll, typing, focus moves, keyboard navigation, page load,
route changes or idle animation. No sound per element in a cascade: a field of
flipping tiles that each tick becomes hundreds of voices in seconds. If the
interaction can happen many times a minute, it stays silent.

## Default to restraint

A brochure site needs at most one or two sounds, on its most deliberate clicks,
such as the primary contact action: nothing on background clicks, no success
fanfare for sending an email. Ship fewer; a removed sound is never missed. A worked
example of what to keep and what to cut is in
[when-to-use.md](better-sound/when-to-use.md#calibration-a-restrained-brochure-site).

## Match weight to the action

Weight is duration, loudness, layer count and pitch movement together. A toggle
gets a 5 to 15ms tick, a send or copy gets a two-note rise under 200ms, only a
real achievement (a purchase, a finished long task) earns a short chord. Sound
length tracks action length: an instant action never gets a long whoosh. See
the weight table in [when-to-use.md](better-sound/when-to-use.md#weight).

## Inform, never punish

Errors use a soft, lower or falling tone, not a buzzer. A sound that makes people
flinch at their own mistake trains them to mute the product. Rising intervals
read as confirm or on, falling as dismiss, off or problem.

## Keep one sound family

Share timbre, pitch set and envelope character across every sound, the way icons
share a stroke. Pick pitches from one scale (a pentatonic set stays consonant in
any order), and set relative levels on purpose: a secondary sound sits several
dB under the primary one. Paired sounds mirror each other: toggle on and off use
the same notes in opposite order.

## Never let sound be the only cue

Every sound has a visible equivalent in the same moment: a state change, a toast,
an icon. Many people browse muted, deaf or hard of hearing, or on a device whose
silent switch is on. Remove the sound in your head; the interface must still say
everything.

## Respect the listener's settings

Start subtle: a master volume around 0.3 and summed layer gain under about 0.6.
Treat `prefers-reduced-motion: reduce` as a reason to stay silent by default,
not as a substitute for a real preference. Ask the platform to mix with the
listener's own audio and honor the silent switch where it allows. Never autoplay
sound, and never play it before a user gesture.

## Offer a toggle when sound is a feature

An app, game or tool that plays sound more than a handful of times per session
needs a persistent sound toggle, and a volume control when sounds are frequent,
both independent of system volume. A site with one or two short sounds on
deliberate clicks, silent under reduced motion, may skip the toggle; adding more
sounds removes that exemption.

## Play on time or not at all

Feedback must land within a few tens of milliseconds of the action. Preload or
prepare sounds after the page loads, so the first click never waits on the
network, and drop a sound that would play late rather than let it arrive detached
from its cause. Engine, preloading and cleanup are in
[playback.md](better-sound/playback.md).

## Reuse one audio context

Create one `AudioContext` lazily on the first gesture, resume it when suspended
and route everything through one master gain that holds the user's volume.
Disconnect nodes when each sound ends. Patterns and pitfalls are in
[playback.md](better-sound/playback.md).

## Prefer small procedural sounds

Synthesized UI sounds weigh nothing, load instantly and stay tunable. Use filtered
noise for clicks and taps, oscillators with a pitch sweep for tonal pops and
confirmations, and a filter to give either its character. Shape every sound with
an explicit envelope and exponential decay. Recipes, parameter ranges and a
"too harsh / too long" translation table are in
[synthesis.md](better-sound/synthesis.md).

## Verify by listening and by numbers

Play every sound at the default volume on laptop speakers and earbuds, repeat it
rapidly, and trigger it under reduced motion and with the toggle off. For each
sound, render it offline and check audible duration, peak level and summed gain
against the weight table; for a flow, count sound events and maximum overlapping
voices. Report anything you could not hear as `Not verified`.

## Before you finish

| Mistake | Fix |
| --- | --- |
| Hover, typing or scroll sound | Remove it; keep the visual feedback |
| Every tile or list item ticks | One sound for the gesture, or none |
| Fanfare for a routine action | Drop to a tick or a two-note rise |
| Sound with no visible change | Add the visible state in the same frame |
| New `AudioContext` per play | One lazy shared context and master gain |
| First click silent or late | Prepare after load, resume on gesture, drop late plays |
| Linear fade to zero, clicks at the end | Exponential decay to 0.001, set the start value first |
| Gain above 1, or layers summing loud | Master around 0.3, layers summed under 0.6 |
| Sound plays under reduced motion | Default it off, and offer a toggle if sound is frequent |

For reviews, use [the shared review format](better-ui-review.md#report).
