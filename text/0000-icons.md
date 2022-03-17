- Start Date: 2021-03-14
- RFC PR:
- TVA Issue:

# Summary

Bundle icons as inline SVG into a simple, organized structure, ready for import and use in an application.

Icons are formatted and optimized by automated build scripts.

By default, the icon's color is inherited from the `color` property of the parent element.

# Basic example

## React (JSX)

Import SVG as JSX element (default) from the library and use in your application.

```jsx
import { bookmark } from '@pluralsight/icons'

const BookmarkIcon = () => <span style={{
  display: "inline-block",
  width: "14px",
  height: "14px",
  color: "#F00"
}}>{bookmark}</span>
```

# Motivation

To provide a single library for all of our icons in a simple, flexible format that is also accessible and performant.

# Detailed design

SVG icons provided by designers are programmatically optimized and the syntax is modified based on the use case (such as removing `xmlns` attributes for inline).

To allow for flexible control over color, `currentColor` is assigned to the fill and/or stroke colors as appropriate (assuming single-color icons. All others can be exempted from this step).

Icon files are then converted to a more framework-specific syntax (jsx by default) mirroring the source directory structure with one additional folder to specify the framework (e.g., `/category/name` for React jsx, `/svelte/category/name` for Svelte, etc.).

Naming and organization should be consistent with, and communicate the design intent of the icons in order to guide proper usage.

Accessibility attributes should be provided with fitting content such that it supports the base scenario.  In this case, a descriptive `aria-label` is sufficient.

Any additional, structured styling would be provided by the `headless-styles` package.

# Drawbacks

Implementing for each framework means supporting multiple formats, increasing up-front and maintenance effort for each additional framework and potentially less flexibility/visibility from the client side.

This can be partially mitigated by exposing the source SVGs and making them available for import directly.  That way, even if a framework is not yet supported, they can still use the icons.

Since this library provides *just* the icon, existing libraries would need to retrofit to replace the SVG portion of their icon components.

# Alternatives

What other designs have been considered? What is the impact of not doing this?

## Doing nothing

Teams will maintain their own icon libraries, which significantly multiplies maintenance efforts and introduces a high probability of quality issues and design dissonance.  This could also make it more difficult for developers to locate icons when they need them.

## Raw SVG assets - direct import

Make SVG files available using the `files` property of package.json

- Leaves implementation entirely up to consumer
- Most frameworks and bundlers have tools to simplify support of the imports
- Can possibly include as an option with almost no extra effort on our part

## Exported string - full SVG

- Requires extra work to implement in most frameworks
- Directly rendering string to markup is a security concern

## Convert SVG attributes to JS Object

- Requires additional steps to implement
- Syntax for specifying children differs per framework
- Children as string has same concerns as full SVG as string

## Font icon

- Performance and quality reduced compared to inline
  - Slighty fuzzier due to font rendering and antialiasing
  - Slightly slower than inline (but only slightly)
  - Separate, larger CSS and font files
    - Can be cached
    - More benefit when more icons used
    - Font file always includes all - can't be reduced through treeshaking (possibly per application with additional build, but not practical)
- Allows greater control from CSS
- Easily used in any environment
- Icon dimensions controlled with `font-size` instead of `height` and `width`
- Accessibility can be improved with ligatures/codepoints, otherwise needs to be handled by implementer
- Broad support
- Can be hosted on a CDN and used for applications beyond web development

## Symbol Sprite

- Can create custom symbol containing only icons currently in use
- Similar to inline SVG
- Most useful when the same icon is used many times, but benefit is small and render slightly slower than inline.

## Embedded svg in css (`image-mask`)

- Specified using data-url
- Not supported in IE, and no good fallback
- Can control color with CSS
- Increased CSS size mainly due to vendor prefixed rules

## Embedded svg in css (`background-image`)

- Cannot control color (except by modifying the SVG)

## As an image (provide src, alt, and class attributes)

- using data-url (no need to base64 encode)
- No real benefits vs inline
- More limited than inline
- May [render fastest](https://cloudfour.com/thinks/svg-icon-stress-test/)
- Cannot specify color with CSS
  - Can change SVG props via JS in the markup, but that creates theming and maintenance issues

# Adoption strategy

Existing implementations would replace the icon content with that from TVA.

# How we teach this

This import will replace a direct SVG import, and the contents can be assumed to be an SVG element that is directly usable by your framework.

# Unresolved questions

How are we sharing icons for other platforms, such as mobile native?

How do we make the icons availables for designers to use?
