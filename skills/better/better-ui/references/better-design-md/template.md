# DESIGN.md template

A skeleton that follows the [DESIGN.md format](https://github.com/google-labs-code/design.md/blob/main/docs/spec.md)
(`alpha`). Copy the block below into the project root as `DESIGN.md`. Replace
every `<…>` with a value taken from the code, and give it a source comment.
Delete sections, tokens and components the project does not have, and list
deliberately absent sections in `omitted`. Remove the HTML comments before
finishing. Schema limits and extension-token rules are in [format.md](format.md).

````markdown
---
version: alpha
name: <Project name>
description: <One sentence: what this design system is for>
# omitted:
#   - section: rounded
#     reason: "<Why the project has none>"
colors:
  primary: "<value>"        # <--css-var>, <path/to/tokens.css>
  neutral: "<value>"        # <--css-var>
  # <role>: "<value>"       # one token per role the code actually uses
  # <role>-dark: "<value>"  # dark-theme value, if the project has a dark theme
typography:
  headline-lg:
    fontFamily: <Family>    # <where the font loads>
    fontSize: <px|rem>      # largest size if fluid; clamp() goes in prose
    fontWeight: <number>
    lineHeight: <number>
    letterSpacing: <em>
  body-md:
    fontFamily: <Family>
    fontSize: <px|rem>
    fontWeight: <number>
    lineHeight: <number>
spacing:
  <step>: <px|rem>          # <--space-*>
  gutter: <px|rem>
rounded:
  <step>: <px|rem>          # <--radius-*>
components:
  button-primary:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.neutral}"
    typography: "{typography.body-md}"
    rounded: "{rounded.<step>}"
    padding: <px|rem>
  button-primary-hover:
    backgroundColor: "<value or {token}>"
---

# <Project name> design

This file records <Project name>'s own design decisions. For general design,
UI and UX guidance, use the `better-ui` skill. When this file and the code
disagree, the code wins: fix this file.

## Overview

<!-- Personality and audience in two or three sentences, in the user's own
terms from the direction questions. Name one specific
reference the design evokes (an object, place or print genre), not adjectives.
Say what the design refuses to be. -->

## Voice

<!-- Optional project section. Tone, person (we/you), language(s), casing,
punctuation habits, words to use and avoid, with one real example from the site. -->

## Colors

<!-- One line per token: descriptive name, {colors.token}, its job, and where it
never appears. Describe how dark mode switches (media query, attribute). -->

- **<Name>** {colors.primary}: <role and limits>.
- **<Name>** {colors.neutral}: <role and limits>.

## Typography

<!-- Families and why. For each level: where it is used. Fluid sizes as the
exact clamp() from the code. Casing, numerals, OpenType features in use. -->

## Layout

<!-- Grid or composition model, max width, reading measure, breakpoints, page
gutters, and how the spacing scale maps to relationships (within group vs
between sections). -->

## Elevation & Depth

<!-- Shadow tokens in a yaml block if the project uses them, or what replaces
depth in a flat design (borders, tone, spacing). -->

```yaml
elevation:
  <level>: "<box-shadow value>"   # <--shadow-*>
```

## Shapes

<!-- Radius scale and where each step applies, border widths, the logo or mark
and its construction, any signature shape. -->

## Motion

<!-- Optional project section. Durations and easings from the code, what animates
and what never does, reduced-motion behavior. -->

```yaml
motion:
  <name>: <ms>                    # <--duration-*>
  easing: "<cubic-bezier(…)>"     # <--ease-*>
```

## Sound

<!-- Optional project section. Which actions play sound, the file or synth that
makes it, volume, default on/off, and how it respects user settings. Delete it
if the project is silent. -->

## Components

<!-- Only components the project ships. For each: purpose, variants, and the
states hover, focus-visible, active, disabled, loading or selected that exist.
Put borders, shadows and transitions here in prose. Link the source file. -->

### <Component>

- Source: `<path/to/component>`
- States: <hover …>, <focus-visible …>, <disabled …>

## Do's and Don'ts

<!-- Five to ten project-specific guardrails. Each one should be impossible to
copy unchanged into another project's DESIGN.md. -->

- **Do** <rule rooted in this project>.
- **Don't** <the generic default an agent would reach for here>.

## Open questions

<!-- Optional project section. Direction questions the user skipped, and gaps
between stated intent and shipped code. Delete it when empty. -->

- <Question or conflict, with the current code value>
````
