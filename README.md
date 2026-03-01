# backstay.rules

## Intro

The premise of **backstay.rules** is to define a single standard approach to working with SCSS that can be implemented across various stack and projects. This repository contains ready to use boilerplate - structure of directories, some useful styles, stylelint config and SKILL.md file.

You can red more about this idea [here](https://kodem.dev/how-to-organize-scss-workflow-introduction-to-backstay-rules/).

## Principles

### Component-oriented approach

This idea should be implemented in an environment focused on component-based approach and reusability. Of course, the structure of files or naming itself can be used freely, but the greatest benefits of the entire approach will be achieved when the entire project structure is well-thought-out. Both things should go hand in hand, and then using this set of rules will become even more natural.

Additionally, for many developers, an advantage of some frameworks over native CSS/SCSS is the management of unused CSS code - deleting a component/view = deleting its styles. Using this approach, we achieve the same thing, but without the need for an additional "layer of abstraction."

### Files structure & boilerplate

Below you can see the basic structure of the main SCSS file directory, along with its contents.

#### `base` directory

**Required** - this directory contains the most general style layer: normalize/reset file, base global styles, and typography styles. This folder also contains one of the most important files - `_layers.scss`, which defines layers and their order.

The `@layer` CSS at-rule was used for better organization and to solve potential problems with overriding styles, especially in large projects. Thanks to this, we emphasize the cascading style.

#### `vendors` directory

**Optional** - directory for external, vendors styles, like slider libraries.

#### `utils` directory

**Required** - directory for all "abstract", global things, definied for specific project: variables, mixins, extends, keyframes.

#### `views`, `partials`, and `components` directories

**Optional** - directories for styles dedicated to specific views, sections/partials, layout areas, and components. All of these folders are optional and depend on the stack used. In many approaches, some or all of them are unnecessary, because the styles for specific components or partials are defined either directly in the component files (as in Vue.js, React) or are located in partial/component directory (as in custom Gutenberg blocks for WordPress).

#### main `styles.scss` file

**Required** - file that is finally compiled into an output file and contains all the necessary "imports" of the previously mentioned files. Looking at the base structure, you can see that throughout the solution we use the `@use` rule instead of the deprecated `@import`.

### File Contents

Above we identified which folders are required and which are optional above. So let's take a closer look at the contents of the required folders.

#### `base` directory

- `_layer.scss` - **required** - a file absolutely crucial to the whole idea of ​​working with cascading styles. It defines layer names and their order from least specific to most*. So, we start with layers responsible for normalization, then vendors, utils and global layers, and then layers for views, partials, and components.
  *Finally, we have the `overrides` layer, which we should avoid, but will sometimes be necessary. Of course, it's used to define styles that override styles defined in other layers.
- `_normalize.scss` - **required** - a file used within the normalize layer to standardize and refine default styles across browsers
- `_base.scss` - **required** - a file used within the defaults layer, used to define the most global styles, mainly for body elements, such as background color.
- `_typography.scss` - **required** - a file used within the defaults layer to define fonts and global typography in the project.

#### `utils` directory

- `_extends.scss` - **optional** - a file used within the utils layer to define global style sets that can be extended to individual elements. This file is optional, but highly recommended in a reusable code approach.
- `_modifiers.scss` - **optional** - the file used within the utils layer is an extension of the \_extends.scss file, where we create global modifier classes based on prepared extenders.
- `_keyframes.scss` - **optional** - the file used within the utils layer, where we define global keyframes.
- `_mixins.scss` - **required** - the file used within the utils layer, containing mixins that can be used throughout the project, simplifying the use of media-queries (along with the definition of breakpoints) and container queries.
- `_variables.scss` - **required** - the file used within the utils layer, containing declarations of global project variables, such as color variables.

That's all of the required stuff. All other directories are optional and depend on the project's needs and the stack used. Regardless of whether you use the remaining folders in the main directory or elsewhere, it's necessary to operate within them in the appropriate layer, which is defined in the `_layers.scss` file – for example, in the button component styles file, all styles should be placed within components layer.

### Names and approach

#### CSS class names

The starting point is the aforementioned BEM methodology, but with some changes:

- **The main class (block)** - as with BEM, it should be as simple and short as possible. For example, in the case of a navigation block, we should use the navigation class.
- **The child (element) class** - should be as short as possible and contain as few nestings as possible. We don't create an element class by going through each element's parent. In the case of the aforementioned navigation block, which can contain a list, a list item, and a link, we use classes from the `navigation__items`, `navigation__item`, and `navigation__link` schema instead of, for example, `navigations__items-item-link`.
- **Modifiers** - this is the biggest difference from the standard BEM methodology. Instead of creating modifier classes using the native, BEM approach like `navigation--modifier`, in this approach, we add modifiers as a separate class with a single hyphen at the beginning `-`. For example, `navigation -modifier`. This eliminates the need to repeat the component class each time, which shortens the class length. Furthermore, we can use globally defined modifiers, such as `-g-container`.
- In a component-oriented approach, the structure itself should be designed to avoid the need for nested classes. If the navigation component is contained within, for example, a partial with a header class, we either don't need to assign it any additional class, or we can use just a single `header__navigation` class added to the navigation class, allowing for additional styling within the header partial. Ideally, we should rely on only one nested class, thanks to the division into partials and components.

#### Variables, Extends, Modifiers, Keyframes

For all variables, extends, and modifiers (as with CSS classes), we should use the self-explanatory approach. Additionally:

- Color variables should contain the term color, the color name, and optionally its shade in a numerical value, where `400` is the base shade in the project, `300`, `200`, etc. are lighter shades, and `500`, `600` are darker shades (if needed).
- We should use CSS variables instead of sass variables in all places where it’s possible. Sass variables should only be used when required for their use, e.g. in mixins.
- For the names we should use the kebab case convention - words are separaed by hyphens.

#### Global and local scopes

Modifiers, like variables, can be defined globally or locally. Global modifiers are intended to contain a set of certain CSS rules that are repeatable in the project. Let's assume we have multiple components containing the same border-radius, border, background, and the same padding. Instead of repeating this each time in the code, we can create a global `@extend` and assign it to the global modifier. Depending on our needs, we can use this extend later, either directly in the styles of, for example, a component, or by adding a global modifier to the component.

For the global variables, extends and modifiers we should use `g-` prefix at the begining of the name. This prefix indicates that a given element is a global element, such as a global color variable or a global modifier. This approach allows us to easily distinguish whether a given variable, for example, is global or defined within a given class.

Local Modifiers and variables should be defined within a given element and only operate within that scope, e.g., the `-type-secondary` modifier for the second variant of the button component.

**Important:**
Modifiers, especially global ones, shoudn't contain only a single CSS property unless it is specific, such as a complex gradient definition used across multiple design elements. Global modifiers are inherently intended to **bind multiple values ​​together** within the design system to avoid code duplication and potential changes in multiple places. Creating modifiers that correspond to only one simple CSS property is not good practice within **backstay.rules** - if you have such needs, it's probably better to use Tailwind or, especially, Atomic CSS, that promote this approach.

#### Container queries > media queries

Within **backstay.rules**, the promoted approach is using container queries instead of media queries, which fits perfectly with the component-oriented and reusable approach. The base version of the boilerplate includes container mixins (`@include container(breakpoint, mode, name)`, e.g., `@include container(lg)`), which facilitate writing styles using the container query approach.

Of course, the rationale for using container queries over media queries in many cases depends on the implemented design and lies with the developer. If the design wasn't prepared with certain assumptions in mind, this approach will be more problematic and time-consuming, and using media queries will be more optimal for the design.

#### Units

We should operate with relative units - rem and em. For HTML, the size is set to 10px, which allows for easy conversion to rem, where the base body size is 1.6rem = 16px.

#### Ordering & formatting

CSS properties should be organized by specific order. Starter from properties connected with item content, positioning (position, top, left etc.), display property and its derivatives (align-items, flex-grow etc.), size properties, margins & paddings, typography properties, appearance properties (backgrounds, borders), ending with properties responsible for transformations and animations.

It’s hard to keep everything in mind, so it's a great way to use a linter to do it for us. In the boilerplate you can find a **stylelint.config.js** file, where the correct order has been defined, there are also some other already prepared settings that makes our SCSS files more readable and unified.
