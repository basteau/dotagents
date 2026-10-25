# Interface writing

Clear and brief beats clever; consistent beats varied. This reference writes and reviews labels, errors, empty states, confirmations and the terms that run through them. The best error message is the interaction redesigned so the error cannot happen.

A deliberate brand voice is not a defect. Raise a departure from plain language only when it creates inconsistency, ambiguity, translation risk or a tone the stakes don't support. Rewording that merely suits your taste is not a finding.

How copy renders (`text-transform`, truncation, smart punctuation) belongs to [better-typography](better-typography.md). Error markup, announcements and the attributes that carry accessible names belong to [better-accessibility](better-accessibility.md). Room for translated strings belongs to [better-layout](better-layout.md). When feedback appears and how a flow recovers belong to [better-interaction](better-interaction.md); the destructive color belongs to [better-colors](better-colors.md).

## Inventory the existing strings first

Before writing or reviewing, find where the copy lives: the translation call (`t(`, `i18n.`, `<FormattedMessage`, `$t(`) and the locale files it reads (`locales/**/*.json`, `*.po`, `*.strings`), or a CMS or API. Read the copy around the change and list the noun for each object and the verb for each action ("project" or "workspace", "Delete" or "Remove"), plus the case per element type and any content style guide.

New copy uses the terms on that list. A synonym is a finding only where the same thing is named two ways.

## One voice, one vocabulary

The product has one voice and its existing copy establishes it; a local edit does not invent a new one. If it's "Archive" in the menu, it isn't "Move to storage" in the toast. A multi-step flow uses one vocabulary: "Get started" to enter, either "Continue" or "Next" to advance, "Done" to finish. Tone flexes with the stakes:

| Context | Tone |
| --- | --- |
| Success, onboarding, empty states | Warm, can be light |
| Routine actions, settings | Neutral, minimal |
| Errors, destructive confirmations | Calm, plain, zero playfulness |
| Data loss, security | Serious, explicit |

## Address the reader directly

In instructional copy, write "you", not "the user". In errors, "we" reads as deflection: prefer "Unable to load content. Check your connection and try again." An established first-person voice can stay in low-stakes copy. Use possessives sparingly ("Favorites" beats "Your favorites") and never mix perspectives in one flow, such as "My account" beside "Your settings".

## Plain words over clever ones

Choose words a tired reader gets on the first pass, and delete every word that does no work. No idioms, colloquialisms or humor that won't translate. Skip unnecessary gender: "Subscribers can post recipes", not "his or her recipes". Match the input device: "tap" on touch, "click" with a pointer, "select" when both are possible.

## Use the words of the trade

Name things the way the people doing the work do: "Waiting on the customer", "Off sick", "Due on the 28th", not a template's generic "Items", "Records" or "Status". Ask of each label and empty state whether any product could print it unchanged; if so, sharpen it with this product's own nouns.

## Build strings whole

Never assemble a sentence from fragments around a variable (`"You have " + n + " new messages"`); word order changes per language. Write one message with placeholders and use the locale's plural rules, not `n === 1 ? "" : "s"`, because many languages have more than two forms. Format numbers, dates and lists with a locale-aware formatter (`Intl` on the web), never by hand. Recipes: [patterns.md](better-writing/patterns.md#plurals-and-placeholders).

## Verb-first buttons

A button label starts with a verb naming the action: "Send", "Save draft", "Delete project". Never "OK!" or "Let's go!". An icon-only button's accessible name follows the same rule: "Delete project", never the glyph ("Trash icon").

## Links describe their destination

Link text makes sense out of context, because screen-reader users navigate by a list of links. Write "Read the billing docs", never "Click here". A bare "Learn more" breaks as soon as two share a page; suffix each: "Learn more about exports".

## One capitalization policy

Pick title case or sentence case per element type and apply it to every instance. Sentence case is the default where the project has no policy. "Save Changes" beside "Discard changes" reads as sloppiness.

## Settings describe the ON state

Label a toggle for what happens when it is on. "Send read receipts" lets users infer off; "Don't send read receipts" makes the toggle a double negative. Link straight to a referenced setting ("Notification settings") rather than describing the path to it.

## Errors say how to fix, next to where it broke

An error is an instruction, and it sits beside the field that failed:

| Bad | Good |
| --- | --- |
| That password is too short | Choose a password with at least 8 characters |
| Invalid date | Enter a date as DD/MM/YYYY |
| Oops! Something went wrong. | Unable to save. Check your connection and try again. |

No blame, no "oops", no exclamation marks. Phrase hints positively ("Use at least 8 characters"). Show a known requirement as helper text before input, not only in the error. When the same error keeps firing, redesign the interaction.

## Confirmations name the action and object

When to offer undo instead of a dialog is [better-interaction](better-interaction.md#prefer-undo-to-confirmation)'s; the undo copy names the result: "Project archived. Undo". A confirmation, kept for irreversible, shared or bulk destruction, is answerable without reading the body:

- The title names action and object, "Delete 'Q3 report'?", never "Are you sure?".
- The body says what is lost, with counts where they apply.
- The buttons are verb plus object, `Delete report` and `Cancel`, never `Yes` and `No`.
- For an account, workspace or repository, ask the person to type its name.

Templates: [patterns.md](better-writing/patterns.md#destructive-flows).

## Empty states point forward

An empty state says what this place is and how to fill it, and offers one next action:

| Bad: a shrug | Good: orientation plus a next step |
| --- | --- |
| "No results." | Title "No projects yet", line "Projects keep your tasks and files together.", button `Create a project` |

A filtered empty state names the query and offers an exit: "No results for 'quarterly'. Clear filters". Never park persistent information there; it disappears once content exists. Loading, saving and success copy: [patterns.md](better-writing/patterns.md#status-copy).

## Placeholders show an example

A placeholder shows a realistic value in the accepted format, `name@example.com` or `DD/MM/YYYY`, never an instruction. It never replaces the visible label [better-accessibility](better-accessibility.md) requires.

## Before you finish

| Detection pattern | Fix |
| --- | --- |
| `Click here`, a link reading `here`, two identical `Learn more` links | Name the destination |
| `Oops`, `Something went wrong` or `!` in an error | Say what failed and the next step |
| `" + n + "`, a template literal holding a fragment, `=== 1 ? "" : "s"` | One message with a plural argument, such as ICU |
| `OK`, `Yes` or `No` on a dialog button; `Are you sure` | Verb plus object; name the action and object |
| `successfully`; `Please` in a routine instruction | Cut it: "Changes saved" |
| Toggle label starting `Don't`, `Disable` or `Hide` | Describe the ON state |
| `aria-label="Trash"`, `"Close icon"`, `"X"` | Name the action: "Delete project", "Close dialog" |
| A date or number formatted with no locale, or assembled from parts (`getMonth()`) | A locale-aware formatter such as `Intl.DateTimeFormat`, given the user's locale |
| `Save Changes` beside `Discard changes` | One case per element type |
| `Items`, `Records`, `Data`, `Status` as a heading or column label | The product's own noun for the thing |

## Reporting

**Severity.** `HIGH`: copy misleads, an error names no way to recover, or a destructive action has neither confirmation nor undo. `MEDIUM`: broken voice, terminology or capitalization consistency. `LOW`: isolated wording polish.

**Verification.** Source alone is enough. Check every label against the action it invokes, every error for a stated fix and every term against the inventory; read locale files as well as components. Report server- or CMS-supplied strings you could not see as `Not verified`.

For reviews, use [the shared review format](better-ui-review.md#report), with Before and After copy in the correction.
