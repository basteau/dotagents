---
name: better-ui
description: "Create, refine and review beautifully crafted web interfaces: pages, landing pages, app screens, components, motion, sound, accessibility and a project's DESIGN.md. Use when building, redesigning, polishing or critiquing UI, or when a page looks generic."
disable-model-invocation: false
---

# Better UI

Build interfaces whose composition, content, and behavior feel deliberately designed
for the product. Keep the user's visual direction and the project's platform,
components, and tokens. Treat aesthetic recipes as starting points; accessibility
requirements and functional correctness remain constraints.

Route by the requested outcome. Read the smallest relevant set of Markdown
references, then follow supporting links only when needed:

| Prompt concerns | Read |
| --- | --- |
| Landing page, homepage, marketing, portfolio, studio, small-business or event site, campaign, launch, offer | [better-landing-page](references/better-landing-page.md) first |
| Create a screen, flow, app screen or dashboard, redesign, “make it beautiful,” explore directions or variants, overall craft | [better-craft](references/better-craft.md) first |
| Decisions, forms, feedback, loading, optimistic updates, undo, perceived speed, prefetching, laws of UX | [better-interaction](references/better-interaction.md) |
| Keyboard, focus, semantics, ARIA, screen readers, forms, hit areas, reduced motion, zoom, WCAG | [better-accessibility](references/better-accessibility.md) |
| Grouping, spacing scale, alignment, reading order, responsive layout, overflow, RTL | [better-layout](references/better-layout.md) |
| Labels, errors, empty states, confirmations, status messages, tone, terminology, plurals | [better-writing](references/better-writing.md) |
| Fonts, font loading, type scale, wrapping, numerals, OpenType features, truncation, text rendering | [better-typography](references/better-typography.md) |
| Palettes, ramps, tokens, OKLCH, gamut, themes, contrast, semantic color | [better-colors](references/better-colors.md) |
| Surfaces, radii, shadows, borders, image outlines, icons, optical alignment, pseudo-elements, view transitions | [better-polish](references/better-polish.md) |
| Transitions, easing, springs, enter/exit, component motion, gestures, motion performance, effect names | [better-animation](references/better-animation.md) |
| UI sound, audio feedback, Web Audio, sound toggles | [better-sound](references/better-sound.md) |
| Mobile web or PWA feel, tap feedback, input zoom, keyboard, viewport units, safe areas, scroll ownership, browser chrome | [better-pwa](references/better-pwa.md) |
| Start a new site's style, or write or update a project's DESIGN.md | [better-design-md](references/better-design-md.md) |
| Review, audit, critique or design score, “looks generic or AI-made,” change or PR review, stress tests with worst-case data | [better-ui-review](references/better-ui-review.md), plus the relevant domains |

When writing or reviewing stylesheets, also read [better-css](references/better-css.md);
in a Tailwind CSS v4 project, also read [tailwind](references/tailwind.md).

Combine domains when the task crosses their boundaries. A label rewrite needs
writing; an overflowing label also needs typography or layout. Broad creation
starts with better-craft; landing pages start with better-landing-page. Each loads
domain guidance as its decisions arise. A project's DESIGN.md, when present, wins
over these defaults; a new site without one decides its style through
better-design-md first. Cross-domain reviews start with better-ui-review; focused
reviews use their domain. “Make this better” authorizes scoped improvements. Infer
the surface from the current task; ask only when missing context would materially
change the work.

Build or fix when requested, answer focused questions directly, and keep reviews
read-only unless fixes are requested. Review reporting sections apply only to
reviews. Verify changes with the project's relevant checks and rendered states;
state what could not be verified. Report shared causes once.
