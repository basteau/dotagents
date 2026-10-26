# Change reviews

Scope, classification and report additions for reviewing a branch, pull request or
uncommitted change. Severity, consolidation and the verdict stay in
[better-ui-review](../better-ui-review.md). Correctness, tests, security and
performance belong to the project's general code review; name such a concern once and
move on.

The author is asking "did I make this worse?" Report what the change caused and stay
mostly quiet about what it merely touched. Read the whole diff before forming a view;
a skimmed diff produces findings the next hunk already fixed.

## Resolve the target

| Target | Resolves to |
| --- | --- |
| Uncommitted work | `git diff HEAD` plus untracked files from `git ls-files --others --exclude-standard` |
| Staged work | `git diff --cached` |
| Current branch | `$BASE...HEAD`, where `$BASE` is `git merge-base origin/<default> HEAD`, plus uncommitted work, counted separately |
| Pull request | Fetch `pull/<n>/head:refs/remotes/pr/<n>`; diff against the PR's own base from `gh pr view <n> --json baseRefName,baseRefOid` |
| An explicit range | The dots the user wrote: `a..b` compares endpoints, `a...b` compares from the merge base |

With no target, stop at the first match: `HEAD` is ahead of the merge base, so review
the branch plus uncommitted work; the tree is dirty, so review uncommitted work;
neither, so there is no change.

Find the default branch from `refs/remotes/origin/HEAD`, then `gh repo view --json
defaultBranchRef`, then a local `main` or `master`, stating the assumption. Record
the base and head refs with their SHAs, and cite lines against the head ref, which
need not match the working tree.

Traps that fail quietly:

- Two dots on a branch report every upstream commit that landed on the base as part
  of the change. Use the merge base unless the user wrote a range.
- `git diff HEAD` omits untracked files, so a new component drops out of a scope the
  report claims to cover.
- A pull request into `release/2.0` diffed against `main` picks up everything between
  the two.
- Mid-rebase or mid-merge, `git diff` succeeds and returns something that is not the
  change. Check `git rev-parse --git-path` for `rebase-merge`, `rebase-apply`,
  `MERGE_HEAD` and `CHERRY_PICK_HEAD`, and stop if any exists.
- A shallow clone returns no merge base. Deepen the fetch and retry, then report the
  scope as unresolvable.
- A moved file is a move, not a delete plus an add; only its genuine edits are in
  scope. Lower the rename threshold with `--find-renames=40%` when it was also
  edited.

## With no change, ask rather than invent one

Never fall back to `HEAD~1..HEAD` on your own; the last commit is often a merge or
someone else's work. State the branch, the commit count ahead of the base, the last
commit's SHA and subject, and any open pull request. Then offer: the open pull
request first when there is one, the last commit by SHA, a target the user names, or
a whole-surface audit, which is a normal review without change statuses. Never report
a review of nothing as Approve.

## Exclude machine-authored files

Exclude lockfiles, snapshots and test reports, build output and source maps,
generated sources and client code, vendored code, and binary media. Apply the
exclusions as pathspecs and name them in the scope block, so the file count is the
reviewed count:

```bash
git diff --name-only "$BASE" <head-ref> -- . ':(exclude)pnpm-lock.yaml' ':(glob,exclude)**/dist/**'
```

`*.lock` misses `package-lock.json` and `pnpm-lock.yaml`, and `**/dist/**` without
`glob` misses a root-level `dist/`; compare the counts with and without the
pathspecs. Two exceptions stay in scope through the code that references them: an
added or swapped font is a typography change, and an added image is a polish and
accessibility change.

## Expand to the blast radius

A changed file is evidence, not the review subject. Review the surfaces it renders
in: one hop to direct importers and callers by default, and a second hop for tokens,
theme values and shared primitives, where one line reaches the whole product. For a
token, search its name, since consumers reference the name rather than import a file.

Search the reviewed ref, not the working tree: `git grep -e '<pattern>' <head-ref>`,
then read hits with `git show`. Order consumers by route and layout entry points
first, then importer count, then proximity, and expand about five in total. State how
many you did not expand; an unstated cutoff reads as completeness.

## Read the removed lines

Regressions are invisible in the post-change state. Read the `-` side of every hunk
for these signals:

| Removed | Owner | Check |
| --- | --- | --- |
| `aria-*`, `role=`, `alt=`, `<label>`, `for` / `htmlFor` | [accessibility](../better-accessibility.md) | A name, description, announcement or association was lost |
| `<button>`, `<a>`, landmarks or lists replaced by `div` / `span` | accessibility | Keyboard and assistive behavior traded for styling |
| `:focus-visible`, `outline`, `tabindex` | accessibility | The focus indicator or tab stop is gone |
| `prefers-reduced-motion` | [animation](../better-animation.md) | Motion now ignores the user's preference |
| `prefers-contrast`, a token swapped for a literal or a lighter token | [colors](../better-colors.md) | Measure the rendered contrast pair |
| Logical properties swapped for `left` / `right` | [layout](../better-layout.md) | Direction-aware layout dropped |
| `lang`, `dir`, `text-wrap`, `line-clamp`, `overflow-wrap`, `tabular-nums` | [typography](../better-typography.md) | Language, direction, wrapping or figures silently changed |
| A user-facing string deleted or shortened | [writing](../better-writing.md) | A label, error or empty state lost information |

These replacements clear the signal: `aria-label` giving way to `aria-labelledby` on
visible text; an explicit role dropped because the element became native; `outline`
replaced by a ring on the same `:focus-visible` selector; `tabindex="0"` dropped from
a now-focusable element; a literal replaced by a token of the same value; a physical
property replaced by its logical one; a string moved into the translation catalogue.

```bash
git diff -U0 "$BASE" <head-ref> -- . <exclusions> \
  | grep -E '^-' | grep -vE '^--- ' \
  | grep -E 'aria-|role=|alt=|htmlFor|for=|focus|outline|tabindex|prefers-|lang=|dir=|text-wrap|line-clamp|overflow-wrap|tabular-nums|-inline|-block'
```

Leave `<head-ref>` off when the target includes uncommitted work. A hit is a lead,
not a finding: read its surrounding hunk, and report it as a Regression only when
nothing in the change replaces it and the owning domain confirms the loss. Removed
strings and swapped tokens have no pattern; read those hunks.

## Classify by cause, not location

- **Introduced:** the change created it.
- **Regression:** the change weakened something previously correct.
- **Pre-existing:** present before the change and not made worse by it.

A finding on an untouched consumer of a changed token is Introduced or Regression. A
finding three lines from a hunk that the change did not cause is Pre-existing. Check
a line's origin with `git blame -L <n>,<n> "$BASE"..<head-ref> -- <file>`, where a
`^` prefix marks a line unchanged since the base; for uncommitted work, drop the
range and look for `Not Committed Yet`.

## Hold the change to its intent

Read the pull request title and body, the linked issue and the commit subjects, and
check the interface delivers what they claim. This finds the incomplete change, which
a surface review misses because it inspects only states that exist:

- A new variant, size or theme missing some of hover, focus, active, disabled,
  loading and selected.
- A new user-facing string with no entry in the project's translation catalogue.
- A new component with no empty, loading, error, disabled or narrow-width state.
- A control added to one surface but not to the siblings that carry its peers.

Do not report scope creep; whether a change does too much is a process question.

## Keep the checkout untouched

`git fetch` writes only to `.git` and is fine. `gh pr checkout`, `git checkout`, `git
switch` and `git stash` rewrite files the author has open, so never run them; read
other revisions with `git show <ref>:<path>`. Rendered verification is opt-in: render
uncommitted work and the current branch in place, and any other target in a temporary
`git worktree` removed afterwards. Otherwise mark visual and runtime claims Not
verified.

## Scope block

Open a change review with this, then the coverage and findings from [the
report](../better-ui-review.md#report), adding a Status column:

| Field | Value |
| --- | --- |
| Target | Uncommitted, staged, branch, `pr 482` or the range as entered |
| Base ref | `origin/main` at `a1b2c3d` |
| Head ref | `refs/remotes/pr/482` at `e4f5g6h` |
| Commits | 7 committed, 2 files uncommitted |
| Files in scope | 12 after exclusions |
| Excluded | `pnpm-lock.yaml`, `src/__snapshots__/` |
| Surfaces expanded | `CheckoutPage`, `SettingsPanel`; 3 further `Button` consumers not expanded |

A domain with no evidence in the change is `Not reviewed: no evidence in the change
scope`, a coverage statement rather than a gap. With no Introduced or Regression
findings, state "No actionable UI findings in this change."
