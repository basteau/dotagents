# DESIGN.md format notes

Schema limits, extension tokens and tooling for the
[DESIGN.md
format](https://github.com/google-labs-code/design.md/blob/main/docs/spec.md).
The format is versioned `alpha` and still changing; the keys, warnings and
commands below describe it as last checked, so confirm them against the current
spec before relying on one.

## Sections

| # | Section | Holds |
| --- | --- | --- |
| 1 | Overview (or Brand & Style) | Personality, audience, one specific reference |
| 2 | Colors | `colors` tokens and each color's role and limits |
| 3 | Typography | `typography` levels, families, where each level is used |
| 4 | Layout (or Layout & Spacing) | Grid, measure, breakpoints, `spacing` scale |
| 5 | Elevation & Depth | Shadows, or what replaces them in a flat design |
| 6 | Shapes | `rounded` scale, borders, the mark or signature shape |
| 7 | Components | `components` tokens and states, in prose too |
| 8 | Do's and Don'ts | Project-specific guardrails |

The section-order lint checks only these eight, so project sections (Voice,
Motion, Sound, Iconography, Open questions) may sit between them. List
deliberately absent sections in `omitted`, with a reason.

## Extension tokens

The schema has no keys for shadow, motion or sound. Put those tokens in a fenced
`yaml` block inside their own section, under keys such as `elevation`, `motion`
or `sound`. The linter warns about them (`token-like-ignored`) and `export` drops
them; that is acceptable, because they exist to give agents context. Never define
the same top-level key in two blocks; the spec rejects the file.

## Values

- Dimensions accept only `px`, `em` and `rem`. For fluid type, record the
  `clamp()` expression in prose and set the token to its largest size.
- Colors accept any CSS color, so keep the project's notation.
- Name dark-theme values as their own tokens (`surface-dark`) and describe the
  switch (media query, attribute, class) in prose.
- Refer to tokens in prose as `{colors.primary}` so prose and values stay linked.

## Components

Component properties are `backgroundColor`, `textColor`, `typography`, `rounded`,
`padding`, `size`, `height` and `width`. Model variants and states as related
keys (`button-primary`, `button-primary-hover`). Describe borders, shadows and
transitions in prose; as token properties they raise unknown-property warnings.

## Lint and diff

The reference CLI is published on npm as `@google/design.md`. Where Node is
available, for example:

```sh
npx @google/design.md lint DESIGN.md
```

Lint checks broken token references, a missing `primary`, section order and
component contrast. A `diff` command compares two versions' tokens; check the
CLI's help for its arguments in the installed version. Without the CLI, check
the same four things by hand and say that lint was not run.
