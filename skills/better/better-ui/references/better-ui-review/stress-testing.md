# Stress testing and state coverage

Render a component in every state it can reach and under the realistic worst case for
every value it renders, then report what visibly broke. Findings use [the shared
report](../better-ui-review.md#report); each fix follows the domain that owns it.
This observes rather than judges: a finding is something that broke on screen, never
"spacing feels tight".

## Scope one component

Test one component per run. "The settings page" spans several; list the candidates
and ask which one. Restate it in one sentence: what it accepts, what it renders and
where it lives in production.

## Map what varies

Read the component, its props and its data hooks, and list every value it renders:

| Field | Source | Type | Limit | Optional |
| --- | --- | --- | --- | --- |
| `name` | `member.name` | string | 255 (`schema.ts:14`) | No |
| `role` | `member.title` | string | unbounded | Yes |
| `count` | `workspace.memberCount` | int | none | No |

Include the values people forget: counts in headers, relative timestamps, badge and
status text, labels and tooltips that come from data, avatars, and the length of the
list itself. Take limits from the validation schema, database column, API types and
`maxLength`. A form that caps at 50 characters but saves to a 255-character column
will meet the longer value through an import or the API. No limit anywhere is itself
a Fragile finding.

Then list the states, which are the branches the code already has:

| Kind | States |
| --- | --- |
| Data | Loading, empty, error, one item, typical set, long enough to scroll or paginate, partial results, stale data refetching |
| Account | Role and permission checks, plan tier, owner versus member, the current user in the list |
| Feature | Flags, trials, limits reached, locked or disabled features |
| Control | Hover, focus, active, selected, disabled, pending |

A state the code cannot reach is not a state; do not add a branch to render one.
Where the design shows a state the code lacks, list it as missing. Combine kinds only
where the code branches on the combination, never as a cross product.

## Keep only axes whose cue matches

| Axis | Run it when | Breaks usually belong to |
| --- | --- | --- |
| Content length | The component renders text it does not author: user input, CMS, API data, translations | [typography](../better-typography.md), [layout](../better-layout.md), [writing](../better-writing.md) |
| Content shape | That text can come from users or locales the team does not write in | typography; mirroring in layout |
| Quantity | The component repeats over items | layout; the empty state in writing |
| State | The component has the state, per the list above | [accessibility](../better-accessibility.md), [interaction](../better-interaction.md), [polish](../better-polish.md) |
| Container | Always: the component never chooses its container's width | layout |
| Environment | The project supports the mode | [colors](../better-colors.md) for dark mode; accessibility for zoom and motion |

A text input gets length and state, never quantity; a fixed-label icon button gets
container and environment only. Write the kept scenarios down before building, one
line each, and name the dropped axes in one line so a wrong inference is cheap to
catch.

## Build the harness

- **Real component, real app.** A scratch route inside the app imports the real
  component untouched and inherits its layout, fonts, global styles and providers.
  Never rebuild a lookalike. Where the framework splits server and client components,
  make the page client code, or fixtures can vanish at the boundary.
- **Change the data, not the component.** Feed scenarios where the component receives
  data: props first, then the app's own seam such as a seeded query cache, a mock
  provider or the project's request mocks. Never hand-edit markup or CSS to produce a
  break. Where the only way in is editing the component, ask before adding a seam.
- **Add nothing else.** Labels, container widths, fixtures and a switcher are all the
  page adds. No fonts, styles, simulated themes or token swaps; a component observed
  under those is a different component.
- **Pick a layout.** Stack every scenario in one column, each labelled and in a
  fixed-width container, for a single look at all of them. Use a switcher instead
  when states need working on one at a time: a dev-only control that sets a URL
  parameter, so every state is a link, with arrow keys stepping through states and
  one key hiding it for screenshots. Style it plainly outside the design system, so
  it does not read as part of the product.
- **Hold pending states still.** Loading stays loading until the switcher moves,
  never resolving on a timeout.
- **Keep it dev-only and contained.** One folder for page, fixtures and switcher; it
  never ships. With no app to host it, use one self-contained HTML file with the
  component and every dataset inline.

## Build the worst case

Every value must be something a real user could produce, or the actual limit the
schema accepts. `aaaaaaaa` and 5,000-character names prove nothing, and the designer
will rightly stop listening. Do not stop at long text either: the breaks that ship
are the one-letter name, the missing avatar, "1 members" and the empty list.

Put one worst-case fixture beside the demo data, shaped exactly like it. Spread
different failures across the first rows, as real data does, instead of stacking them
all on row 1. Add separate Empty, One and Huge cases. Use `example.com` and `.test`
domains so no fixture points at a real inbox.

| Category | Values that break things |
| --- | --- |
| Names | `Aleksandra Wiśniewska-Kowalczyk`; `Jo` and `J`; `Christopher Alexander Montgomery III` (naive initials give "CI"); `Đặng Thị Ngọc Hân` (stacked diacritics clip); `王秀英` (no spaces); `نور الهدى عبد الرحمن` (RTL); `👩🏽‍💻 Priya` (slicing splits the emoji); `  Sam   Lee `; missing, with only an email |
| Identifiers | `bartholomew.fitzgerald@northwind-industries-holdings.example.com`; `a@b.co`; a 100-character URL with a query string; a UUID; `Q3 Board Deck — FINAL (revised) v12.pdf`, where end truncation hides the part that differs |
| Labels and copy | `Benachrichtigungseinstellungen` (unbreakable compound); French or German expansion of a short label; twelve tags, or one 45-character tag; empty or whitespace-only title; `<script>`, `&amp;` and `**bold**` that must render literally; a newline in a single-line field; a 2,000-character paste |
| Numbers | `0`, `1` (plurals), `1284` (separator), `1000000` (badge width), `-42.5`, `0.1 + 0.2` rendered raw, `142%` past a meter's bounds, `null` / `NaN` printed literally, `1.284` in `de-DE`, a live value crossing `99` to `100` |
| Collections | 0 items; 1 item; exactly the page size and one more; 1,000+ unpaginated; one item ten times larger than the rest; items with identical names |
| Time | Now ("0 seconds ago"); 12 days, 11 months and 3 years ago; a future date; `1970-01-01`; a timestamp near midnight in another timezone; a duration that never rolls up |
| Media | An avatar URL that 404s; no avatar; a 4000×200 or 200×4000 image; a dark transparent logo on a dark theme; a slow image with no reserved size |
| States | A raw API error message; partial optional fields across one list; every enum status at once; no permission; the current user in the list |

Format numbers, plurals and dates with the platform's locale APIs, such as
`Intl.NumberFormat`, `Intl.PluralRules` and `Intl.RelativeTimeFormat`, rather than
hand-built strings.

## Check the environment

Check the worst case at the real container width first, since a sidebar list is not a
full-page list, then at 320px, squeezed by a flex or grid sibling, and at the widest
supported layout. Fixed-width containers on the page cover widths in one load.
Viewing modes are the viewer's to toggle rather than something the page simulates:
200% zoom or a larger root font size, dark mode, `dir="rtl"`, reduced motion, and a
touch device, where hover-only actions are unreachable. Invite the user to tab
through the instances for focus.

## Look once

Load the page once, skim every scenario and note what visibly broke, each as a
one-line note under its scenario label so the page reads as the report on its own. A
scenario missing the content it was fed means the harness is broken, not the
component; fix the plumbing first. With no browser at hand, say so, hand over the URL
and scenario list, and report no observed findings; risks reasoned from the CSS go in
as inferred, not observed.

## Read the failure signature

| What you see | Usual cause | Fix |
| --- | --- | --- |
| Avatar or icon squished into an oval | Fixed-size flex child shrinking | `flex-shrink: 0` on the fixed box |
| Text escapes its box instead of wrapping | Flex or grid child with `min-width: auto` | `min-width: 0`, or `minmax(0, 1fr)` in grid |
| Email or URL runs past the edge | No break opportunity in the string | `overflow-wrap: anywhere` on that element |
| Trailing action pushed off-screen | Middle content took all the space | `min-width: 0` on the middle, `flex-shrink: 0` on the action |
| Badge wraps to two lines | Badge allowed to shrink | `white-space: nowrap; flex-shrink: 0`, and decide what yields instead |
| Avatar adrift beside a three-line name | Centered alignment on rows that can grow | Align to the first line once text can wrap |
| Heading breaks mid-word | `word-break: break-all` | `overflow-wrap: anywhere` |
| Wrong initials or `�` | Splitting on spaces and taking code units | Grapheme-aware initials, first plus last word, fallback icon |
| Orphaned dash or blank line | Placeholder for a missing optional field | Omit the line, or reserve its height on purpose |
| "1 members", `NaN`, `1284` | Hardcoded plural or raw number | Locale-aware plural and number formatting, guarding null |
| Numbers jitter or columns misalign | Proportional figures | `font-variant-numeric: tabular-nums` |
| Translated label overflows | Fixed width on a text container | Width from content with a `min-width` |
| Diacritics clipped top or bottom | Tight line height with `overflow: hidden` | Looser line height; no clipping on text boxes |
| Broken-image icon | No error fallback | Fall back to initials; `object-fit: cover` |
| Ellipsis with no way to the full text | Truncation and nothing else | Full value in a tooltip, expansion or detail view |
| Last row cut mid-glyph | Fixed-height container with no affordance | Visible scrollbar or fade, and intended `overflow` |
| Escaped markup shown, or markup executed | Wrong escaping layer | Escape once at render; never inject user data as HTML |
| 1,000 rows stutter | Every row rendered | Virtualize or paginate, and say which |

Each long string forces a choice, made per field: wrap what identifies something,
such as names and detail titles; truncate the end of secondary metadata whose start
carries meaning; truncate the middle where items differ at the end, such as file
names, paths and hashes; clamp previews in cards; never truncate numbers, dates or
anything the user compares. Where more than one answer is right, list it under
Decisions for you.

## Rate and report

- **Broken** is HIGH: content unreadable, an action unreachable, or wrong data shown.
- **Ugly** is MEDIUM or LOW by reach: readable but visibly wrong, such as a squished
  avatar or wrapped badge.
- **Fragile** is LOW unless the escalation triggers say otherwise: fine now but one
  realistic step from breaking, such as no length limit or no fallback. Cite the
  source evidence.

Put the worst-case value in each finding's Before. List what held up, which proves
the test was real and tells the user what not to touch. "Everything survived" plus
the scenario list is a complete report; do not pad it with preferences. Say where the
harness runs and which states it has.

Report before fixing. On a request to fix, follow the owning domain, then flip
through every state again, demo data included, since a fix for the worst case must
not regress the typical one. Keep the worst-case fixture as a regression test only if
the user wants it. Leave the harness up until the user is done, then delete its
folder and search for the route and fixture names so nothing of it remains.
