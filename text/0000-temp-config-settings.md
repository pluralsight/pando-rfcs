- Start Date: 2023-08-14
- RFC PR: (leave this empty)
- Pando Issue: (leave this empty)

# Before Reading

Please approach this document with a generous perspective understanding that it
is a Markdown file so all examples are _theory_ and **have not been prototyped**.
You may see "gotchas" which is normal.

If you choose to contribute, please address the overall purpose of the RFC and not
nit-picking the "gotchas" in code examples. :heart:

# Summary

Migrate Headless-styles to use [Panda CSS](https://panda-css.com/) under the hood via
a new public Pando preset configuration option called `@pluralsight/panda-preset`.

This will allow both Headless-styles and the React lib to be more flexible and
configurable for future use-cases by leveraging the power of Panda CSS. Additionally,
it will increase the overall performance of the React lib by reducing the amount of
CSS that is shipped to the client (via Atomic-CSS).

# Basic example

This is a basic example of using a new Pando CLI tool to install and setup the
local project with all dependencies and configuration needed to use Pando with
Panda CSS (I know that's a lot on the brain to read - I think they are trolling
us with the name :stuck_out_tongue_winking_eye: ).

```bash
$ pnpx pando setup --typescript

📥 Installing Pando dependencies

- @pluralsight/headless-styles
- @pluralsight/icons
- @pluralsight/react

📥 Installing Panda CSS dependencies (dev)

- @pandacss/dev
- postcss
- @pluralsight/panda-preset

🚧 Setting up Pando configuration

...creating pando.config.js
...creating postcss.config.cjs

🚧 Seting up tsconfig.pando.json

...creating tsconfig.pando.json (Pando recommneded settings for TS)
```

Then, once setup, you should be able to use all the recipes, tokens, patterns, etc.
that is specific to Pando and Panda CSS.

```tsx
import { Button } from '@pluralsight/react'
import { hstack } from '../styled-system/patterns'

export default function FormButtons(props: { pending: boolean }) {
  return (
    <div className={hstack({ gap: '4' })}>
      <Button sentiment="danger" type="button">Cancel</Button>
      <Button
        bg={brand.discord.blurple}
        endIcon={props.pending ? LoadingIcon : null}
        type="submit"
      >
        Save
      </Button>
    </div>
  )
}
```

Above the example is using the `hstack` pattern from the `styled-system`
package. This is a pattern that is specific to Pando and Panda CSS. It is not
something that is available in any Pando package.

Additionally, the example is using a custom `brand` token from the Panda config.
This is a token that the user has added to the Panda CSS config. It is not
something that is available in any Pando package.

Last the example is using the `bg` prop that comes from Panda CSS. This
is not something that is available in any Pando package.

# Motivation

The motivation behind this RFC is to allow both the Headless-styles and React
libs to be more flexible and configurable for future use-cases by leveraging the
power of Panda CSS (i.e. Panda is basically what HS end-goal was to be).

## Why are we doing this?

There is an issue in our Company FE tech (big "C") regarding the lack of structure
and consistency of tooling (especially with styling). This has led to unnecessary
chaos and potential bugs in our codebase. For example, the home page of
Pluralsight.com is loading 1.5MB of CSS to the client. Likewise, it's also using
mutliple style libraries.

From a basic check of the library page (single page), it looks like we are using the following
style libraries:

- Bootstrap
- Tailwind
- Glamor
- Styled-components/CSS Modules
- Headless-styles

We need to solve this problem. :scream:

## What use cases does it support?

This RFC will allow us to support the following use cases:

1. Teams that need more flexibility and configurability than what Pando
currently provides.
2. Teams that prefer to use a CSS-in-JS solution.
3. Teams that prefer _not_ to use a CSS-in-JS solution.
4. Teams that use a client-side framework (any library).
5. Teams that use a server-side framework (any library).

## What is the expected outcome?

The expected outcome is that we will have a more flexible and configurable
solution in Pando for teams to use while creating structure and standardization
around the style tooling that we use across the company. In result, this should
lead to a more performant and consistent experience for our customers and developers.

Additionally, it should help team members be more productive and efficient by
reducing the amount of time they spend on styling and debugging styling issues.
This will also allow a more successful transition for teammates that are moving
from one team to another (regardless of the product).

...or...a major brand update. :wink:

# Detailed design

The following design assumes you are familiar with the
[Core Features and Concepts](https://panda-css.com/docs/concepts/cascade-layers) of Panda CSS.

## Pando Preset

The Pando preset will allow us to reduce the amount of configuration that is required
to use Pando today, while also allowing us to be more flexible and configurable for
future use-cases.

The preset would use the following global config options:

### preflight - `true`

This would allow us to eliminate the need for our `normalize` setup which would
be instead handled by this and other options in the preset.

### prefix - `pando`

This would create all tokens to use the `--pando-<token>` naming to stay on brand.

### minify - `false`

This should be used in whichever way each team needs depending on their
build/project use-case. Ideally, each team should use something like this:

```js
minify: process.env.NODE_ENV === 'production'
```

### include - `[]`

Same as above. Ideally, each team should use something like this:

```js
include: ['./src/**/*.{js,jsx,ts,tsx}']
```

### exclude - `[]`

Same as include.

### conditions - `Extendable<PandoConditions>`

Here we will define all the conditions for Pando usage. An example would be:

```js
conditions: {
  light: `[data-color-mode="light"] &`,
  dark: `[data-color-mode="dark"] &`,
  system: `[data-color-mode="system"] &`,
  pando: `[data-theme="pando"] &`,

  extend: {
    showNav: '&:is([data-show-nav="true"])'
  },
},
```

This allows our theming to be even more flexible for a feature set by allowing the
user to choose a `mode` and a `theme` option. For example, in a global nav/app-bar,
the user can always toggle "light" or "dark" mode, but in their profile settings
they can also choose any theme (i.e. "pando", "pizza-site", "classic", etc.).

### globalCSS - `Extendable<PandoGlobalStyles>`

This is where all remaining normalize styles would go that is not handled by
recipes or patterns.

### theme - `Extendable<PandoTheme>`

This is where all the tokens would go. This would be the same as the current
`design-tokens` package except use a object structure that has a better developer experience.

```js
bg="default.surface.weak"
bg="action.bg"
bg="danger.bg"
borderColor="info.border"
borderColor="warning.border"
borderColor="success.border"
```

Another example using the Panda `css` pattern:

```jsx
<Button className={css({
  _hover: {
    bg: 'action.bg-hover',
  },
})} />
```

This would also define our fonts (both PS and code).

### utilities - `Extendable<PandoUtilities>`

This is where all the utilities would go. Here is an example of a good one that
creates a shorthand prop for `padding-inline` we would include:

```js
pse: {
  className: 'padding-inline-start',
  values: 'spacing',
  transform(value: CssValue<string>) {
    return {
      paddingInlineStart: value,
      paddingInlineEnd: value,
    }
  },
},
```

With the above, you can now streamline your code by using the following:

```jsx
<div className={css({ pse: '4' })} />
```

### patterns - `Extendable<PandoPatterns>`

This is where all the patterns would go if we need them. I can't think of any
off the top of my head, but I'm sure we will need some?

### jsxFramework = `react`

This will allow Panda to know how to build the jsx components for teams that need
a custom solution outside of our React library.

> :warning: **Note:** These components will **not** be accessible. It is therefore
> recommended that teams use our React library for the "end all solution".

### jsxFactory = `pando`

This will create the jsx components to use the naming convention of:

```jsx
<pando.div />
```

# Drawbacks

With any change, there are tradeoffs. Especially when trying to create structure
where there is none.

## Why should we _not_ do this?

- implementation cost, both in term of code size and complexity
- whether the proposed feature can be implemented in user space
- the impact on teaching people a Pando library
- integration of this feature with other existing and planned features
- cost of migrating existing Pando applications (is it a breaking change?)

### Implementation cost

This is dependant on the team. Right now, we are seeing that each product team
manages their project and tech stack in it's own way which leads to a lot of duplicate
code and unnecessary complexity.

What is meant by this is that Skills, Flow, and Social and Hands-on Learning all have their own
way of managing their styles including teams within product teams. This means that
there is no way of really measuring the implementation cost on the Pando side
because there is currently no standadard.

For the sake of the argument, let's say that the implementation cost is high.

> :warning: **Note:** This is ultimately the goal to resolve via Pando. The more
> we can standardize the way we do things, the more we can reduce the implementation
> cost and the more we can focus on the product.

### User space implementation

Right now, every team uses React across the company (outside of one or two Skills
BC's) so this is not a concern. Likewise, most teams are using CSS-in-JS so this
enhancement should be easy to pick up and migrate. For the most part, teams should
be reducing code by adopting and using Pando React.

Panda :panda: is becoming _very_ popular across the across the industry for this exact
reason. It is easy to pick up and use.

### Impact on teaching

Documentation shouldn't be too affected by this change. The only thing that would
change is the way we teach the installation process and how to use Pando React
via the new customizations it will offer.

### Integration with other features

No different here than just getting teams to use the Pando React library over
the current chaos. Once again, this is a good thing because it will allow us to
create a more consistent experience (both customer and developer) across the company.

### Cost of migration

This would introduce a breaking change **only** for teams that have yet to adopt
Pando React.

For teams that have already adopted Pando React, this would be a
non-breaking change since the tooling is internal and it's just adding some deps
and a new config file(s) (i.e. panda.config.ts and postcss if not already in use).

For teams that have not adopted Pando React, this would require them to migrate
their code to use Pando React and Panda CSS/PostCSS. This migration cost here is
almost the same as the cost of migrating to Pando React. The only difference is
we are creatin a new standardized approach to styling which will make it easier
for teams to adopt and use while being more performant for the customer.

If we approach this via the CLI promoted above, it should be even easier for teams
to start using Pando without doing any work.

# Alternatives

## Pando team specific

The alternative is to continue to do what we are doing now which is basically
doing what Panda already does so we can streamline the process and make it easier
by just migrating to Panda.

## Company wide

The alternative is to continue to do what we are doing now which is basically
allowing all teams to do whatever they want for FE styling which has led to a
lot of unnecessary complexity and issues on different levels across the company.

# Adoption strategy

This would (and should) require us to officially sunset any internal UI libary outside of
Pando (Classic, ps-mui, and anything else). This would be a good thing because it would
allow us to focus on one library and make it the best it can be for the company.

This process has already begun and is in the works for the next few months via our
ops team.

# How we teach this

This change shouldn't change the way we teach Pando. It should only make it easier
to teach Pando. We would need to update the installation instructions to include
the CLI and everything it will do for the teams.

Likewise, the only thing that should change on the Reference documentation is the
customization sections which should be updated to reflect the new structure and
show off the flexibility.

# Unresolved questions

- How can we continously audit the usage to ensure consistency across the company? Can we create a GH action or something similar to prevent shipping a branch that installs a new lib?
- How can we ensure that teams are using the CLI and not just doing their own thing?
- How can we make the developer experience better for teams that use Pando? Browser extensions? VSCode extensions? Other?