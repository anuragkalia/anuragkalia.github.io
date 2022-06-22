---
layout: post
title: "CSS Cascading Rules: Notes from Specifications"
date: 2022-05-18 12:00:00 +0530
---

Two types of rules:

1. Style rules: `.a > .c {}`
2. at-rules: `@import`, `@layer` etc.

# @import rules

Examples:

1. `@import "narrow.css"`
2. `@import url("narrow.css")`
3. `@import url("conditional.css") supports (display: flex) handheld and (max-width: 400px);`
4. `@import url("conditional.css") supports (not(display: flex));`

Imports replace the `@import` rule with the fetched stylesheet. If `@import`ing the same file multiple times, it is replaced multiple times.

Q: What happens to circular CSS imports?

# Shorthand Properties

1. Sets ALL the longhand sub-properties, even ones which are not explicitly set.
2. `all: initial` resets ALL the CSS properties to their initial values ignoring even the browser stylesheet.

For example:

1. `background: color` resets `background-image` set somewhere else along with the explicitly set `background-color`.
2. `border` resets `border-image`.

How to assign values?

1. A valid value exists for every property of every element in the DOM.
2. Value processing chain for an element

   1. Declared Values: The values of properties applicable to the given element. Maybe none.

      1. Vendor aliasing and legacy shorthands handled here.
      2. Note: Invalid property values are counted as declared value at all. This is important for prefixed values. For example:

         1. ```
            /* Older Safari */
            image-rendering: -webkit-optimize-contrast;
            /* Newer Safari */
            image-rendering: pixelated;
            ```

            Here, `image-rendering`'s decalred value on older Safari browsers is only `-webkit-optimize-contrast`. `pixelated` is a declared value in those browsers.

            In newer browsers, both of them are declaree values but `pixelated` wins because of order of appearance (See below).

   2. Cascaded Value: The winning declared value based on layer, specificity, order of appearance etc. Maybe none if there are no declared value.
      1. Rules
         1. Origin
            1. Transition declarations
            2. Browser with `important`
            3. User with `important`
            4. Author with `important`
            5. Animation declarations
            6. Author
            7. User
            8. Browser
         2. Context
            1. Child shadow DOM with `important`
            2. Parent with `important`
            3. Parent
            4. Child shadow DOM
         3. Element attached styles: `style` attribute in HTML
         4. Layers (`@layer first, last;`)
            1. last with `important`
            2. first with `important`
            3. unlayered with `important`
            4. unlayered
            5. last
            6. first
         5. Specificity
            1. Based on specifity rules (A, B, C)
         6. Order of appearance
      2. TODO: I don't actually understand this in depth. It needs a deep dive.
   3. Specified Value: Cascaded value, or the default value.
      1. CSS global values like `initial`, `revert` handled here.
   4. Computed value: Absolutizes the specified value as much as possible without actually rendering the document. For example:
      1. relative units are parsed.
      2. `smaller`, `bolder` are parsed.
      3. Some `percentages` are resolved.
      4. Relative URLs become absolute.
   5. Used Value: The final absolute value. For example:
      1. `margin: auto` is a computed value but the final margin in pixels is the used value.
   6. Actual Value: Approximations that happen while actualing rasterizing.
      1. For example: `border-left-width: 4.2px` -> `border-left-width: 4px`.

Spec examples are here for visual representation: https://www.w3.org/TR/css-cascade-5/#stages-examples
