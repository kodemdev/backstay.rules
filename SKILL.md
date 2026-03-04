---
name: backstay-rules
description: Apply the backstay.rules SCSS architecture with layered styles, component-oriented structure, and project naming conventions. Use when creating, refactoring, or reviewing SCSS structure, files, class names, variables, mixins, modifiers, and import order.
license: MIT
compatibility: opencode
---

# backstay.rules

## Purpose

Use this skill to keep SCSS code aligned with the `backstay.rules` approach:

- component-oriented and reusable styles,
- clear CSS cascade with `@layer`,
- strict structure and naming conventions,
- unified formatting and property ordering.

## When To Use

Use this skill when the user asks to:

- create or reorganize SCSS architecture,
- add or edit files in `base`, `utils`, `views`, `partials`, `components`, or `vendors`,
- define variables, mixins, modifiers, extends, or keyframes,
- review style naming and layer usage,
- align SCSS with this repository boilerplate.

## Source Of Truth

Follow these files first:

- `styles.scss` for `@use` import order.
- `base/_layers.scss` for the layer order.
- `stylelint.config.js` for formatting and property order.

If those files and user request conflict, ask the user before changing conventions.

## Layer Rules (Mandatory)

Use the global layer order from `base/_layers.scss`:

- `normalize, vendors, utils, defaults, views, partials, components, overrides`

All style definitions must be wrapped in the correct `@layer` block:

- global reset/normalize -> `@layer normalize`
- project variables/mixins/extends/modifiers/keyframes -> `@layer utils`
- global base/typography -> `@layer defaults`
- view styles -> `@layer views`
- partial/layout styles -> `@layer partials`
- component styles -> `@layer components`
- last-resort overrides -> `@layer overrides` (avoid unless necessary)

## Import Rules

In `styles.scss`:

- use `@use`, not legacy `@import` (except vendor CSS files that require import with `layer(...)`),
- keep imports ordered from global/base toward specific styles,

Current canonical import flow:

1. layers
2. utils (variables/modifiers/keyframes)
3. defaults (base/normalize/typography)
4. views
5. partials
6. components

When adding a new file, include it in `styles.scss` in the matching section only if the project stack/build pipeline uses centralized SCSS entry imports. In stacks with colocated component styles (for example React/Vue setups), follow the project's existing style-loading pattern instead.

## Naming Conventions

### Class names

- Prefer BEM-like block and element names (`block`, `block__element`).
- Keep names short and self-explanatory.
- Avoid deep nested element chains.

### Modifiers

- Use separate modifier classes prefixed with single hyphen: `-modifier`.
- Prefer `component -modifier` over `component--modifier`.
- Use global modifiers with `-g-` prefix (for example `-g-container`).
- Local modifiers stay scoped to their component context.

### Global tokens and helpers

- Use `g-` prefix for global variables/extends/modifiers/keyframes names.
- Prefer CSS custom properties (`--var`) over Sass variables where possible.
- Use Sass variables mainly where compile-time behavior is required (for example mixin maps).
- Use kebab-case naming.

### Color variable naming

- Include `color` and semantic name.
- Optional shade system: `400` base, `300/200` lighter, `500/600` darker.

## Responsive Strategy

Preferred approach:

- container queries over media queries for component-level responsiveness.
- use provided mixins from `utils/_mixins.scss`, especially `@include container(...)`.

Media queries are acceptable when required by design constraints.

## Units And Typography

- Use relative units (`rem`, `em`) by default.
- Root font size baseline is `10px`; `1.6rem` maps to `16px`.

## Global Modifiers Guidance

Global modifiers should combine meaningful, reusable rule sets.
Do not create global modifiers for a single trivial property unless it is a specific reusable tokenized case (for example a shared complex gradient).

## Formatting And Order

Follow `stylelint.config.js`:

- property order defined by `order/properties-order`,
- uppercase HEX colors,
- single quotes,
- consistent block spacing/newline rules.

When editing SCSS, preserve lint-friendly formatting.

## Agent Workflow

When implementing SCSS changes, follow this sequence:

1. Identify scope:
   - global foundation (`base`/`utils`) or feature scope (`views`/`partials`/`components`).
2. Select correct file and `@layer`.
3. Add/update styles using naming and modifier conventions.
4. If the project uses centralized entry styles, add/adjust imports in `styles.scss`; otherwise wire styles according to the stack's existing component/style integration.
5. Re-check layering and specificity before adding overrides.
6. Validate formatting and property order against `stylelint.config.js`.

## Guardrails

- Do not flatten the layered architecture.
- Do not replace `@use` with global `@import` patterns.
- Do not introduce utility-class-heavy atomic style conventions in this system.
- Do not remove required files from required directories.
- Do not rename existing canonical files unless user explicitly requests migration.

## Quick Checklist For Agents

- [ ] Correct folder and file selected
- [ ] Correct `@layer` used
- [ ] Naming follows block/element + `-modifier` conventions
- [ ] Global entities use `g-` prefixes
- [ ] Container-query-first responsive logic considered
- [ ] Style file wired using the project's active build pattern (`styles.scss` entry or colocated component imports)
- [ ] Output matches stylelint conventions
