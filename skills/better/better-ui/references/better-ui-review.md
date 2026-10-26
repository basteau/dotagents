# UI review

Review a screen, flow, component or change and return one ranked report. This
reference owns scope, evidence, severity, consolidation and the report format;
reviews are read-only unless fixes are also requested.

Domains own their rules, checks and fixes: [accessibility](better-accessibility.md),
[layout](better-layout.md), [writing](better-writing.md),
[typography](better-typography.md), [colors](better-colors.md),
[polish](better-polish.md), [animation](better-animation.md),
[interaction](better-interaction.md), [CSS](better-css.md), and [landing
pages](better-landing-page.md), [PWA](better-pwa.md) and [sound](better-sound.md)
where present. A focused review loads only its domain.

## Report evidence, not taste

A finding needs evidence; Approve needs the inspection it claims. A density, radius
or voice you would have chosen differently is not a defect, nor is a deliberate
project choice. Render a predicted failure or list it unverified.

## Resolve the scope first

State the screen, flow, component or change, inferred from the request and workspace.
Too large to inspect credibly? Narrow it to one complete flow (the one the request
centres on, else the shared entry path) and name what you excluded.

## Recon the project before judging

Identify the stack, styling system, components, tokens, viewports and preview or test
commands; read the project's interface docs. A documented convention settles taste
but never excuses an accessibility or task failure; report a finding a guideline or
shared token causes once, against it. Write fixes in the project's idiom.

## Review a change for what it caused

Review a branch, pull request or uncommitted change against its base and stated
intent; classify each finding as Introduced, Regression or Pre-existing. Never check
out, switch or stash over the author's working tree.
[change-review.md](better-ui-review/change-review.md) has the procedure.

## Inspect every reachable state

Review every branch the code has: loading, empty, error, one item, typical,
overflowing, partial or stale data, disabled, role and plan variants, and each
control's hover, focus, active and selected states, with realistic product-shaped
data. A designed state the code cannot reach is missing: report, never fake it.

## Stress it with worst-case data

Feed plausible, schema-backed worst cases through the real data boundary at container
width, 320px, 200% zoom, dark mode and RTL if supported; see
[stress-testing.md](better-ui-review/stress-testing.md).

## Verify what can be verified

Render when appearance, input, motion or device behavior decides the result, and
record the exact command or interaction; source cannot prove look or feel, and a
screenshot cannot prove a code cause. Trace shared components and tokens before
assigning a cause. A check you could not run is Not verified: no finding, no Clear.

## Critique the render as a stranger would

For a critique, design verdict or "does this look generated?" check, judge
screenshots at three widths, never the code: make the passes, score with juror
weights and caps, name tells per [critique.md](better-ui-review/critique.md). Report
them as Opportunities, each with its screenshot; a tell or score alone is no finding.

## Rank by user impact

- **HIGH:** blocks a task, misleads, hides essential content or controls, risks data
  loss, or is systemic.
- **MEDIUM:** harms comprehension, efficiency, adaptability or consistency.
- **LOW:** isolated polish with limited task impact.

Once the owning domain confirms one of these, it is HIGH however minor the surface: a
control with no accessible name or visible focus; a path reachable by pointer but not
keyboard; motion that ignores reduced motion; content clipped or unreachable at 320px
or 200% zoom; text failing its contrast ratio; meaning carried by color or motion
alone; a destructive action with no confirmation or undo; truncation with no way to
the full value; hidden content with no visible cue; an error that names no recovery.
Within a severity, rank by reach: a token or shared-component fix outranks one leaf.

## Report each root cause once

One root cause is one finding: every confirmed location, under the domain owning the
rule, noting cross-domain effects in Why. Keep to about 15 findings unless asked for
an exhaustive audit; a cap never drops a blocker and states how many it excluded.

## Prefer the cheaper fix

Severity says how bad a finding is; this picks the fix. Take the earliest that works:

1. **Delete:** a divider space can carry, motion on frequent actions, redundant ARIA.
2. **Use the platform:** the native element, control or focus ring over a rebuild.
3. **Reuse the project:** an existing token, spacing step, curve or component.
4. **Correct the value:** the exact easing, gap or contrast pair the domain gives.
5. **Add:** a new token, wrapper, query or attribute, only when nothing above works.

When the reviewed code adds what deletion would fix, the deletion is the After.

## Report

Start with the scope; a focused review needs one line. A cross-domain or change
review also names the stack, conventions found and boundary, then shows coverage:

| Domain | Evidence inspected | Result |
| --- | --- | --- |
| Accessibility | Files, components, states or checks | Findings count, Clear, or Not reviewed: reason |

Clear requires inspection. Then one table for all domains, by severity, then reach:

| Severity | Domain | Location | Before | After | Why |
| --- | --- | --- | --- | --- | --- |
| HIGH | Accessibility | `src/Dialog.tsx:42` | `<button><XIcon /></button>` | `aria-label="Close"`, icon `aria-hidden` | The icon-only control has no accessible name |

Location is `path/to/file:line`, or the screen and component without source. Before
and After are current code and a concrete fix, never "consider improving"; Why names
the violated principle and its user effect. A change review adds a Status column; a
stress test puts the worst case in Before. No findings: "No actionable UI findings."

Add these sections where they apply:

- **Pre-existing:** for a change review, at most three, outside the verdict.
- **Decisions for you:** a fork such as wrap or truncate, one recommended line each.
- **Opportunities:** for a critique, scores and top three problems in the [critique
  format](better-ui-review/critique.md#write-the-critique-down).
- **What held up:** for a stress test, the worst cases already handled.
- **Verification:** each check, how it ran and its result; Not verified checks apart.

End a readiness or change review with a verdict: **Block** while a HIGH finding
remains (for a change, only Introduced and Regression count); otherwise **Approve**
the inspected scope, naming every Not reviewed domain; **Incomplete** when missing
verification prevents either. A critique may end with ranked opportunities.

## Fix only on request

When fixes are requested, the report is the change scope. Fix at the shared cause
with the owning domain's rules, re-check every affected state and the original data,
and remove any scratch harness only when the user is done with it.

## Before you finish

| Mistake | Fix |
| --- | --- |
| Per-domain sections, or one symptom in several rows | One table by severity, then reach; one row per shared cause |
| A missing name or focus ring rated MEDIUM on a minor surface | Escalation triggers are HIGH on sight |
| A rendering claim from source, or Clear without inspection | Render it, or mark it Not verified or Not reviewed |
| Only the happy path with demo data | Every reachable state with worst-case data |
| An After in another stack, or one that adds a wrapper | The project's idiom; delete or go native first |
| Edits during a review-only request | Report and stop |
