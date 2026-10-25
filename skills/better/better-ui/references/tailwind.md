# Tailwind

Applies to Tailwind CSS v4. Check the installed version before choosing syntax;
features in current documentation may require a newer minor release. Preserve
working configuration, plugins, tokens, and component composition conventions.

## Classes and composition

- Write complete class strings, mapping finite variants to values such as
  `{ error: "text-red-600" }`. For runtime values, use inline styles or CSS
  variables consumed by a static utility such as `bg-[var(--accent)]`.
- Reuse existing tokens; use arbitrary values for one-offs and shared tokens for
  repeated design decisions. Keep repeated UI structure and styling in components
  or template partials; use custom CSS when it expresses the requirement clearly.
- Resolve accidental conflicts while preserving intentional combinations such as
  `p-4 px-6` and breakpoint/state overrides. Class-string order does not control
  CSS precedence; use the project's existing override mechanism.

## Utility equivalents for the domain references

The domain references write plain CSS; these are the v4 utilities that carry the
same rules. Check generated CSS before adding config.

| Rule | Utilities |
| --- | --- |
| Gate hover by capability | v4 `hover:` already compiles to `@media (hover: hover)`; v3 needs `future.hoverOnlyWhenSupported` |
| Name transitioned properties | `transition-[scale,opacity]`; `transition-transform` covers `transform`, `translate`, `scale` and `rotate` |
| Individual transforms | v4 `scale-*`, `translate-*` and `rotate-*` write the individual properties; v3 writes `transform`, so name `transform` there |
| Press feedback | `transition-transform duration-150 ease-out enabled:active:scale-[0.97]`, or the project's press value |
| Reduced motion | Prefix movement, scale and blur with `motion-safe:`; `motion-reduce:` holds overrides for the preference |
| Focus ring that survives forced colors | `focus-visible:ring-2` plus `focus-visible:outline-hidden` (a transparent outline); `outline-none` removes the outline everywhere |
| Visually hidden text, skip link | `sr-only`; `sr-only focus:not-sr-only` on the skip link |
| Decorative layer | `pointer-events-none` |
| Hit area larger than the visual | `relative after:absolute after:top-1/2 after:left-1/2 after:size-11 after:-translate-1/2` |
| Concentric radius | Outer `rounded-*` = inner radius + padding, as `rounded-2xl p-2` around `rounded-lg` |
| Image outline | `outline outline-1 -outline-offset-1 outline-black/10 dark:outline-white/10` |
| Directional icon in RTL | `rtl:-scale-x-100`, and no second flip on the same element |

## Missing styles

Distinguish incomplete class strings, excluded source files, unsupported utilities,
and generated CSS overridden by the cascade. Inspect the generated rule before
changing specificity. When generation is missing, read
[source detection](https://tailwindcss.com/docs/detecting-classes-in-source-files).
Check source paths relative to the stylesheet and the build's scanning base path;
register only the required sources or explicit classes. Dependencies and ignored
files may need `@source`. Confirm the real build generates the expected CSS.

## Theme and custom styles

Read the corresponding documentation when changing these definitions:

- [Theme tokens](https://tailwindcss.com/docs/theme): use `@theme` for tokens that
  expose utilities or variants, ordinary CSS variables for other values, and
  `@theme inline` when a theme token references another variable, except for semantic
  tokens a theme repoints at runtime ([token naming](better-colors/token-naming.md)).
- [Custom utilities and variants](https://tailwindcss.com/docs/adding-custom-styles):
  use `@utility` for custom utilities that need variant support.
- [Scoped styles and directives](https://tailwindcss.com/docs/functions-and-directives):
  when separately compiled styles use `@apply` or `@variant`, use `@reference`
  to access definitions without duplicating CSS. Reference the project stylesheet
  for project-specific definitions, or `"tailwindcss"` for default definitions.
- [Dark-mode activation](https://tailwindcss.com/docs/dark-mode): follow the
  project's existing trigger when changing theme behavior.

When configuration or custom utilities change, verify their output with the
project's Tailwind build. The parent skill owns rendered-state verification.

## Setup and migration

For requested setup or build changes, read the matching
[installation guide](https://tailwindcss.com/docs/installation). New v4 setups use
`@import "tailwindcss"` and CSS-first configuration; existing `@config` integrations
can remain. For requested migrations, read the
[upgrade guide](https://tailwindcss.com/docs/upgrade-guide) and
[compatibility guide](https://tailwindcss.com/docs/compatibility), checking browser
targets, build integration, and changed visual defaults before applying changes.
