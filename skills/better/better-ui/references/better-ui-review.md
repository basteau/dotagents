# UI review

Review a screen, flow, component or change and return one ranked report. This reference owns scope, evidence, severity, consolidation and the report format. It applies only when a review, audit, critique or stress test is requested; build and improvement requests follow their domain guidance. Reviews are read-only unless fixes are also requested.

Domain references own their rules, checks and fixes: [accessibility](better-accessibility.md), [layout](better-layout.md), [writing](better-writing.md), [typography](better-typography.md), [colors](better-colors.md), [polish](better-polish.md), [animation](better-animation.md) and [interaction](better-interaction.md), plus [landing pages](better-landing-page.md), [mobile web and PWA](better-pwa.md) and [sound](better-sound.md) where the surface has them. A focused review loads only its domain and uses the severity and report below. Change scoping is in [change-review.md](better-ui-review/change-review.md); state coverage and worst-case data are in [stress-testing.md](better-ui-review/stress-testing.md); design-quality passes, juror scores and generated-look tells are in [critique.md](better-ui-review/critique.md).

## Report evidence, not taste

The bar for a finding is evidence; the bar for Approve is having inspected what you claim to have inspected. A density, radius or voice you would have chosen differently is not a defect, and a deliberate project choice is not a blocker because a recipe differs. A predicted failure is a risk: render it, or list it as unverified.

## Resolve the scope first

Infer the screen, flow, component or change from the request and workspace, and state it. When it is too large to inspect credibly, narrow it to one complete flow: the one the request centres on, else the entry path every user passes through. Name what you excluded. Never imply that uninspected surfaces were reviewed.

## Recon the project before judging

Identify the stack, styling system, component library, tokens, supported viewports and any preview or test command. Read what the project documents about its interface, such as design-system docs, contributing or agent guides and Storybook. A documented convention settles taste but never excuses an accessibility or task failure; when a guideline or shared token causes a finding, report it once against that source. Write every fix in the project's own idiom.

## Review a change for what it caused

Review a branch, pull request or uncommitted change against its base. Expand the changed files to the surfaces that render them, read the removed lines for lost signals, and classify each finding by cause as Introduced, Regression or Pre-existing. Hold the change to its stated intent and look for the states it forgot. Never check out, switch or stash over the author's working tree. The procedure is in [change-review.md](better-ui-review/change-review.md).

## Inspect every reachable state

The states are the branches the code already has: loading, empty, error, one item, a typical set, an overflowing set, partial or stale data, disabled, role and plan variants, and the hover, focus, active and selected states of each control. Review each with product-shaped data in realistic quantities, because three rows of "Test item" make every state look fine. A state the design shows but the code cannot reach is missing; report it rather than faking it.

## Stress it with worst-case data

Demo data is chosen to make a design look good. Feed the realistic worst case through the boundary real data uses: long and one-letter names, unbreakable emails and URLs, missing optional fields, counts of 0, 1 and ten times typical, translated and RTL text. Check it at the real container width, 320px, 200% zoom, and in dark mode and RTL where supported. Values must be plausible or schema-backed, never random. Axes, values and failure signatures are in [stress-testing.md](better-ui-review/stress-testing.md).

## Verify what can be verified

Inspect the rendered interface when appearance, input, motion or device behavior decides the result, and record the exact command or interaction. Source alone cannot establish visual quality, motion feel or device behavior; a screenshot alone cannot establish a code cause. Trace shared components and tokens before assigning a cause. A check you could not run is Not verified, never a finding and never Clear.

## Critique the render as a stranger would

When a critique, a design verdict or a "does this look generated?" check is requested, judge screenshots at about 1440, 768 and 390px, never the code. Make the passes, score with the juror weights and caps, and name tells in [critique.md](better-ui-review/critique.md). Report them as Opportunities, with the screenshot that shows each; a tell or a low score is never a finding without a broken rule behind it.

## Rank by user impact

- **HIGH:** blocks a task, misleads, hides essential content or controls, risks data loss, or repeats as a systemic failure.
- **MEDIUM:** meaningfully harms comprehension, efficiency, adaptability or consistency.
- **LOW:** isolated polish with limited task impact.

Once the owning domain confirms one of these, it is HIGH however minor the surface: a control with no accessible name or no visible focus; a path reachable by pointer but not keyboard; motion that ignores reduced motion; content clipped or unreachable at 320px or 200% zoom; text failing its contrast ratio; meaning carried by color or motion alone; a destructive action with no confirmation or undo; truncated content with no way to the full value; hidden content with no visible cue; an error that names no recovery.

Within a severity, rank by reach. A token or shared-component fix outranks the same symptom in one leaf.

## Report each root cause once

One root cause is one finding listing every confirmed location, even when it crosses domains. Assign it to the domain that owns the underlying rule and note secondary effects in Why. Keep the report proportionate, around 15 findings unless an exhaustive audit is asked for. A cap may shorten a report but never drop a blocker; say how many findings it excluded.

## Prefer the cheaper fix

Severity says how bad a finding is; this says which fix to propose. Take the earliest that works:

1. **Delete:** the separator that space would carry, the animation on a high-frequency action, ARIA a native element makes redundant.
2. **Use the platform:** the native element, control or focus ring over a custom rebuild.
3. **Reuse the project:** an existing token, spacing step, curve or component.
4. **Correct the value:** the exact easing, gap or contrast pair the owning domain gives.
5. **Add:** a new token, wrapper, query or attribute, only when nothing above works.

When the reviewed code adds what deletion would fix, the deletion is the After.

## Report

Start with the scope. A focused review needs one line. A cross-domain or change review also names the stack, the conventions found and the boundary, then shows coverage:

| Domain | Evidence inspected | Result |
| --- | --- | --- |
| Accessibility | Files, components, states or checks | Findings count, Clear, or Not reviewed: reason |

Clear requires inspection. Then one findings table across all domains, ordered by severity, then reach:

| Severity | Domain | Location | Before | After | Why |
| --- | --- | --- | --- | --- | --- |
| HIGH | Accessibility | `src/Dialog.tsx:42` | `<button><XIcon /></button>` | `aria-label="Close"`, icon `aria-hidden` | The icon-only control has no accessible name |

Location is `path/to/file:line`, or the exact screen and component when there is no source. Before and After show the current implementation and a concrete replacement, never "consider improving". Why names the violated principle and its effect on the user. A change review adds a Status column; a stress test names the worst-case value in Before. With no findings, state "No actionable UI findings."

Add these sections where they apply:

- **Pre-existing:** for a change review, at most three, outside the verdict.
- **Decisions for you:** findings with more than one right answer, such as wrap or truncate, paginate or virtualize; one line each with a recommendation.
- **Opportunities:** for a requested critique, the scores and the top three problems in the [critique format](better-ui-review/critique.md#write-the-critique-down), grounded in the brief and the screenshots, kept apart from defects.
- **What held up:** for a stress test, the worst cases already handled.
- **Verification:** each check, how it was run and its result, with Not verified checks listed separately.

End a readiness or change review with a verdict. **Block** while a HIGH finding remains, counting only Introduced and Regression for a change. **Approve** for the inspected scope otherwise, naming every Not reviewed domain. **Incomplete** when missing verification prevents either. A critique may end with prioritized opportunities instead.

## Fix only on request

When fixes are requested, the report is the change scope. Fix at the shared cause using the owning domain's rules, then re-check every affected state and the original data, not just the case that broke; a fix for one state often breaks another. Remove any scratch harness only when the user is done with it.

## Before you finish

| Mistake | Fix |
| --- | --- |
| One findings section per domain | One table ranked by severity, then reach |
| The same symptom in several rows | One row against the shared token or component, listing every location |
| A missing name or focus ring rated MEDIUM because the surface is minor | Escalation triggers are HIGH on sight |
| A rendering claim backed only by source | Render it, or mark it Not verified |
| A domain marked Clear that was never inspected | Not reviewed, with the reason |
| Only the happy path with demo data | Every reachable state, with worst-case and product-shaped data |
| An After written in a stack the project does not use | Rewrite it in the project's idiom |
| An After that adds a wrapper or attribute | Check whether deleting or the native element fixes it first |
| Edits made during a review-only request | Report and stop |
| A critique read from the code, or a tell filed as a defect | Screenshots at three widths; tells go under Opportunities |
