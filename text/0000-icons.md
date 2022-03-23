- Start Date: 2021-03-14
- RFC PR:
- TVA Issue:

# Summary

>**Brief explanation of the feature.**

## Purpose

- Provide Pluralsight icons for easy use in JavaScript applications.
- Maintain consistent iconography across applications.
- Reduce maintenance efforts and improve efficiency across UI teams.

## Tools

- [svgo](https://www.npmjs.com/package/svgo)
- [Babel](https://babeljs.io/)
  - @babel/plugin-transform-react-jsx

# Basic example

>**If the proposal involves a new or changed API, include a basic code example. Omit this section if it's not applicable.**

## React (JSX)

Import SVG as JSX element (default) from the library and use in your application.

Use `headless-styles` to provide supporting properties for controlling the icon rendering.

```jsx
import { bookmarkIcon } from '@pluralsight/icons'
import { getIconProps } from '@pluralsight/headless-styles'

const BookmarkIcon = () => <span {...getIconProps()}>{bookmarkIcon}</span>
```

# Motivation

>**Why are we doing this? What use cases does it support? What is the expected outcome?**
>
>**Please focus on explaining the motivation so that if this RFC is not accepted, the motivation could be used to develop alternative solutions. In other words, enumerate the constraints you are trying to solve without coupling them too closely to the solution you have in mind.**

## Why are we doing this?

To provide a single library for all of our icons in a simple, flexible format that is accessible and adds minimal overhead.

## What use cases does it support?

When a Pluralsight icon needs to be added to a UI built with JavaScript.

## What is the expected outcome?

A variable containing an inline SVG element that can be placed directly into your markup.

# Detailed design

>**This is the bulk of the RFC. Explain the design in enough detail for somebody familiar with a TVA library to understand, and for somebody familiar with the implementation to implement. This should get into specifics and corner-cases, and include examples of how the feature is used. Any new terminology should be defined here.**

SVG icons provided by designers are placed in the `src/svg` folder, then programmatically optimized using `svgo` and saved to `build/svg`.

To allow for control of the color via CSS by inheriting the foreground color, `currentColor` is assigned to the `fill` and/or `stroke` colors by a build script as appropriate (this assumes single-color icons).

Icon files are then converted to framework-specific syntax.

React is our default, which converts svg to `_jsx` syntax using `svg-to-jsx` and `babel`. React icons can then be imported from `@pluralsight/icons` - since it is the default.

Each framework will have its own corresponding folder. E.g., , `@pluralsight/icons/svelte` for Svelte, etc.

Any additional properties (including styling) can be provided by the `headless-styles` package.

The optimized SVG icons in `build/svg` will also be made available using the `files` attribute of package.json to allow usage by any consumer we don't support.

Since the provided variable represents the icon imagery, and is used as an inline svg (or img) element, we can safely change the underlying implementation without affecting the API.

## Tree shaking

To support tree shaking, each icon is transpiled to its own file.

The index file then individually imports all of the transpiled icons, and exports them collectively.

```js
export { arrowLeft } from 'build/react/arrowLeft'
export { arrowRight } from 'build/react/arrowRight'
...
```

## Usage

The imported element can be assumed to contain a renderable svg (or img) element, and used the same as those elements (as seen in the earlier example)

## Accessibility

Each icon will have, by default, an `aria-label` attribute that describes the icon.

# Drawbacks

>**Why should we *not* do this? Please consider:**
>
>- **implementation cost, both in term of code size and complexity**
>- **whether the proposed feature can be implemented in user space**
>- **the impact on teaching people a TVA library**
>- **integration of this feature with other existing and planned features**
>- **cost of migrating existing TVA applications (is it a breaking change?)**
>
>**There are tradeoffs to choosing any path. Attempt to identify them here.**

# Alternatives

>**What other designs have been considered? What is the impact of not doing this?**

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
- Cannot specify color with CSS
  - Can change SVG props via JS in the markup, but that creates theming and maintenance issues
- May [render fastest](https://cloudfour.com/thinks/svg-icon-stress-test/)

# Adoption strategy

>**If we implement this proposal, how will existing TVA developers adopt it? Is this a breaking change? Can we write a codemod? Should we coordinate with other projects or libraries?**

Existing implementations would replace the icon content with that from TVA.

We will begin by releasing the SVG files, followed by React, Svelte, Vue, and Angular elements.

Since this library provides *just* the icon, existing libraries would only need to replace the SVG portion of their icon components.

For example, here is an icon implementation from the Classic design system before using the new icons package.

```tsx
  // This file is generated by running the "icons" bin script.
  // Manual changes might be lost - proceed with caution!

  import React, { forwardRef } from 'react'
  import Icon, { IconComponent } from '..'


  const BriefcaseIcon = forwardRef((props, ref) => {
    const { 'aria-label': ariaLabel, ...rest } = props

    return (
      <Icon {...rest} ref={ref}>
        <svg aria-label="briefcase icon" viewBox="0 0 24 24"role='img' {...(ariaLabel && { 'aria-label': ariaLabel })}><path fillRule="evenodd" clipRule="evenodd" d="M21 6a1 1 0 0 1 1 1v13a1 1 0 0 1-1 1H3a1 1 0 0 1-1-1V7a1 1 0 0 1 1-1h5V3a1 1 0 0 1 1-1h6a1 1 0 0 1 1 1v3h5zm-1 13H4v-2h16v2zM14 6V4h-4v2h4zm6 2H4v7h16V8zm-8.5 4a.5.5 0 0 0-.5.5v1a.5.5 0 0 0 .5.5h1a.5.5 0 0 0 .5-.5v-1a.5.5 0 0 0-.5-.5h-1z"/></svg>
      </Icon>
    )
  }) as IconComponent

  BriefcaseIcon.displayName = "BriefcaseIcon"
  BriefcaseIcon.colors = Icon.colors
  BriefcaseIcon.sizes = Icon.sizes

  export { BriefcaseIcon }
```

...and here is that same icon implementation using the new icons package

```tsx
  // This file is generated by running the "icons" bin script.
  // Manual changes might be lost - proceed with caution!

  import React, { forwardRef } from 'react'
  import Icon, { IconComponent } from '..'
  import { briefcaseIcon } from '@pluralsight/icons'


  const BriefcaseIcon = forwardRef((props, ref) => {
    const { 'aria-label': ariaLabel, ...rest } = props

    return (
      <Icon {...rest} ref={ref}>
        {briefcaseIcon}
      </Icon>
    )
  }) as IconComponent

  BriefcaseIcon.displayName = "BriefcaseIcon"
  BriefcaseIcon.colors = Icon.colors
  BriefcaseIcon.sizes = Icon.sizes

  export { BriefcaseIcon }
```

Here is an example of using SVG files directly in a React app built with CRA

**This approach is meant to be used for unsupported frameworks**

Note that this requires appropriate application build tooling in order to properly handle the import, which CRA includes by default.

```jsx
import { ReactComponent as BookmarkIcon } from '@pluralsight/icons/svg/bookmark.svg'
import { getIconProps } from '@pluralsight/headless-styles'

// Props are applied to the parent rather than directly to the component
// in order to maintain consistent structural assumptions.
const MyBookmarkComponent = (props) => <span {...getIconProps()}><BookmarkIcon/></span>
```


# How we teach this

>**What names and terminology work best for these concepts and why? How is this idea best presented? As a continuation of existing TVA patterns?**
>
>**Would the acceptance of this proposal mean the TVA documentation must be re-organized or altered? Does it change how TVA is taught to new developers at any level?**

This import will replace a direct SVG import, and the contents can be assumed to be an SVG element that is directly usable by your framework.

When a developer needs an icon asset for their framework, they would only need to know to come to this package, and what it provides.

# Unresolved questions

>**Optional, but suggested for first drafts. What parts of the design are still TBD?**

How are we sharing icons for other platforms, such as mobile native?

How do we make the icons availables for designers to use? And conversely, what is our process for receiving icons from designers?  (Second question to be solved separately from this package, possibly the first as well).
