# Laws of UX as build rules

Each law below is reduced to what to do in the interface and how it gets misapplied.
Use the law as the rationale in a finding; report the user effect, not the law's
name alone. Where another reference owns the implementation, it is linked.

## Decisions and memory

| Law | Do this | Misuse to avoid |
| --- | --- | --- |
| Hick's law | Shrink the choice set at each step: recommend a default, group options, move rare ones behind a labeled disclosure. For long known lists (countries, fonts), add search or typeahead instead of trimming. | Hiding frequent options to look minimal; splitting one decision into many steps |
| Pareto principle | Find the critical path from real usage and give it the prime positions; move the long tail to an overflow menu or settings. | Ranking by what is easy to build or by internal org chart |
| Progressive disclosure | Show what this step needs; reveal advanced controls on demand behind a visible, labeled affordance, and remember its open state. | Disclosing things people need every time; hiding with no cue that more exists |
| Cognitive load | Remove whatever does not help the task: duplicate warnings, redundant labels, decorative icons, extra buttons. Show context instead of asking people to recall it. | Removing information the decision actually needs |
| Miller's law | Chunk long values (`4532 0151 1283 0366`, `+32 470 12 34 56`) and long forms into labeled groups; keep the needed facts visible across steps. | Treating 7±2 as a hard cap on menu or nav length |
| Tesler's law | Decide who carries the irreducible complexity, and give it to the system: smart defaults, safe inference, pickers for constrained values. | Hiding complexity that experts need to control; guessing consequential values |
| Postel's law | Accept harmless variation (whitespace, separators, case, pasted codes) and normalize the output strictly. | Silently reinterpreting ambiguous input such as `03/04` |

## Perception and grouping

Implementation of spacing and alignment lives in [better-layout](../better-layout.md);
surface treatment in [better-polish](../better-polish.md).

| Law | Do this | Misuse to avoid |
| --- | --- | --- |
| Proximity | Put related controls closer to each other than to unrelated ones; labels sit tight to their field. | Uniform spacing that makes everything one group |
| Common region | Give a bounded surface to a set that must read as one unit (a settings section, a card). | Boxing every element, so the boundaries stop meaning anything |
| Uniform connectedness | Connect sequential or linked items visually: a line between steps, a shared color for a selection. | Connecting items that do not share a relationship |
| Similarity | Same function, same look and behavior; different function, visibly different. | Restyling one instance of a shared control for a local effect |
| Von Restorff effect | Give one element per view the distinct treatment: the primary action, or a destructive action set apart from its neighbors. | Emphasizing several things, which cancels the effect |
| Serial position | Put the most important items first or last in a sequence; bury nothing critical in the middle of a long row. | Reordering stable navigation, which breaks learned positions |
| Prägnanz | Prefer the simplest shapes and fewest visual layers that still communicate the structure. | Removing affordances in the name of simplicity |
| Aesthetic-usability effect | Polish raises trust and tolerance for minor friction, so apply it, but test tasks, not looks. | Letting a beautiful screen mask a broken flow in review |

## Input and speed

| Law | Do this | Misuse to avoid |
| --- | --- | --- |
| Fitts's law | Make frequent targets large and near the pointer or thumb that will hit them; let edge-anchored targets reach the edge; keep destructive actions away from frequent ones. Sizes: [hit-areas.md](../better-accessibility/hit-areas.md). | Growing the visible element when only the hit area needed to grow; overlapping hit areas |
| Doherty threshold | Acknowledge input within a frame and answer or show progress within about 400ms; when the work cannot be that fast, use skeletons, retained content, or optimistic updates. See [feedback-and-latency.md](feedback-and-latency.md). | Fake progress that claims work is done; delaying fast responses to show a loader |
| Jakob's law | Use the conventions people learned elsewhere: standard nav labels, link styling, search placement, platform shortcuts. | Custom icon-only navigation or novel gestures with no visible fallback |

## Motivation and memory of the experience

| Law | Do this | Misuse to avoid |
| --- | --- | --- |
| Goal-gradient effect | Show steps completed and remaining, and mark progress as it happens; make the last steps short. | Progress bars that jump backward or invent steps |
| Zeigarnik effect | Surface unfinished work the person cares about (an incomplete setup, a draft) with a direct way to resume, and let them dismiss it. | Guilt banners and nagging used to drive engagement |
| Peak-end rule | Invest in the moment of success and in error recovery; end flows on a clear, in-place result with a next step. | Celebrations on routine actions; an abrupt redirect after the payoff |
