- Start Date: 2023-04-13
- RFC PR: (leave this empty)
- Pando Issue: (leave this empty)

# Before Reading

Please approach this document with a generous perspective understanding that it
is a Markdown file so all examples are _theory_ and **have not been prototyped**.
You may see "gotchas" which is normal.

If you choose to contribute, please address the overall purpose of the RFC and not
nit-picking the "gotchas" in code examples. :heart:

# Summary

Introduce a Pando React library that allows users to have an entry-level starting
point to the ecosystem. This library should serve as a way to help people learn and
become more comfortable with:

1. The concept of the Headless-styles library and how it is used
2. Semantic markup
3. High performance React patterns

Essentially, this library should fulfill the Pando core value of creating a tool
that helps its users become better Javascript and React developers.

## Basic example

For a basic components, like a Button, Field Message, or Input, it should function
something like the following example.

Here is an example of a form:

```typescript
import {
  Button,
  ErrorMessage,
  Input,
  Label,
  FieldMessage,
  FieldProvider
} from '@pluralsight/react'

...

<form onSubmit={handleSubmitForm}>
  <FieldProvider
    id="name"
    invalid={testValidity}
    messageId="[nameMessage, nameErrorMessage]"
    required
  >
    <Label>Name</Label>
    <Input
      placeholder="Enter your name"
      type="text"
      value={value}
    />
    <FieldMessage>Enter your full name.</FieldMessage>
    <ErrorMessage>Please enter your full name.</ErrorMessage>
  </FieldProvider>

  <Button disabled={formNotValid} sentiment="action" type="submit">
    Submit
  </Button>
</form>
```

Each component should be composable (can be used like Lego blocks) to allow maximum
flexibility in creating "custom designs" that either include/remove specific elements.

**This is the current standard supported and promoted by the React team.**

In this example, the `FieldProvider` would act as the `getFormControlProps`
Headless-styles helper in managing the state of the field and other children.

This means, any field related component (i.e. Label, Input, ErrorMessage, etc.)
would naturally inherit the state and all a11y props needed that is managed from
the Headless-styles helpers (invalid, disabled, readOnly, etc.) and require no
props of those from the user.

This would also allow us to internally create error logs that would "make it hard
to break something" which would inform the user when they are missing a FieldProvider.

For a more complex component - see the [Detailed design](#detailed-design) section.

## Motivation

### Why are we doing this?

There has been consistent requests for a React component library due to the nature
of most developers either being either unaware of how to use a "headless" library
or generally uncomfortable with being responsible for owning their own components.

Having a component library that has a familiar interface which ultimately uses
Headless-styles and React-utils under the hood should help break that barrier.

### What use cases does it support?

A component library would be most beneficial to teams/users that:

1. Are mostly Backend developers that have little to no experience using Javascript or React.
2. Junior frontend developers who have little to no experience in Javascript or React.
3. Marketing departments who would like to use Pando, but have "just enough experience to be dangerous" in frontend development.
4. Team leads that haven't been able to keep up with the technology trends but still need to code when possible.
5. Teams that have the experience and understanding of Javascript and React, but don't have the time that may be required to use an API like Headless-styles (vs. dropping in an abstraction of it - component).

### What is the expected outcome?

The expected outcome of creating a component library should be:

1. More adoption for Pando due to having a library that uses a familar pattern that people are generally more comfortable with.
2. Should naturally teach users about the semantic token naming convention as it will inheritently use Headless-styles for prop definitions (which uses the same naming convention of the themes for
consistency).
3. Provide a developer experience that uses "React best practices" for a result that naturally allows scalability with custom designs via composable components, and high performance via advanced React patterns.
4. Share the functionality as Lego blocks (create your own outcome based on what you combine/remove).
5. By nature, it should increase the community contribution for the same reasons
mentioned above (a familiar design).
6. Provide UI and implementation consistency across the platform, making it easier to collaborate across codebases.
## Detailed design

The design of the components library should first and foremost center around the
Pando standards:

1. A11y above all
2. High Perfomance
3. Easy to use

Outside of these standards, there are 2 groups of components to consider:

1. [Simple components](#simple-components) (Tag, Button, TextLink, etc.)
2. [Complex compents](#complex-components) (Alert Dialogs, Notifications, Modal, etc.)

Last, there should also be helpers that naturally compliment the experience:

1. [Custom hooks](#custom-hooks)
2. [Helper functions](#helper-functions)

_:bulb: In addition to React 18, all components should be natively compatible for use
within React Server Components._

### Simple Components

Simple components should mimick the vanilla JSX design React provides with the Headless-styles
props extending it (i.e. not manipulating React props or creating "component
does all" solutions).

For example, a Button should be built something like this:

```typescript
import { getButtonProps } from '@pluralsight/headless-styles'
import type { ButtonOptions } from '@pluralsight/headless-styles/types'
import { type HTMLAttributes, type PropsWithChildren } from 'react'

interface ButtonProps extends ButtonOptions, HTMLAttributes<HTMLButtonElement> {}

function PandoButton(props: PropsWithChildren<ButtonProps>, ref: RefObject) {
  const {
    className,
    disabled,
    icon,
    sentiment,
    size,
    usage,
    ...nativeBtnProps
  } = props
  const pandoProps = getButtonProps({
    className,
    disabled,
    sentiment,
    size,
    usage,
    icon
  })

  return (
    <button {...pandoProps.button} {...nativeProps} ref={ref} />
  )
}

export const Button = forwardRef(PandoButton)
```

By nature, this example uses the native React JSX design while extending the
Headless-styles props on top of it so it can be used in a way that compliments
and teaches users about React & Javascript due to not manipluating any props/React APIs.

This is an example of how the Button above can be used:

```typescript
  <Button type="button" onClick={handleClick} ref={btnRef}>
    <ShareIcon hidden />
    Share
  </Button>
```

### Complex Components

For more complex components, we should be able to lean into the same design above
by holding true to our standard of keeping the library designed in a composable way.
Likewise, most complex components should include a Provider to manage state and/or
data when needed.

For example, a Tabs family would be composable presentational components that
include a Provider for state management only, not the data going into them.

This keeps Pando's responsibility clean meaning that Pando should only be
managing UI states, not the data being used within its components.

_:warning: This would require refactor from the original [Tabs Hooks](https://design.pluralsight.com/docs/reference/react-hooks/useTabs) which are mostly composable, but would need to be
pushed even further for the ideal design. Likewise, they would need to be moved
into the new react library for colocation._

An example of the Tabs family in practice, should look something like this:

```jsx
<Tabs>
  <TabList>
    <Tab>Tab 1</Tab>
    <Tab>Tab 2</Tab>
  </TabList>
  <PanelList>
    <Panel>
      <PageOne />
    <Panel>
    <Panel>
      <PageTwo />
    <Panel>
  </PanelList>
</Tabs>
```

In the above example, the `Tabs` component would internally use a  `TabsProvider`
which manages the active state of the tabs. Notice how the usage mimics the HTML
table family which is naturally composable.

### Custom Hooks

For components that push the complexity even further by requiring the use of `react-dom`
APIs like `createPortal`, the library should include a Provider/hook combination
that allow managing notification and alert dialog components (i.e. Toast, Confirm/Prompt Dialog)
without the need for JSX.

For example, a Toast would combine the use of a Provider that manages its state,
and a hook that provides an easy to use API for the user.

Assuming we start with the current [Toast recommended foundation](https://design.pluralsight.com/docs/next/reference/components/toast#basic-toast), we can simply create a Provider and hook
combo that looks something like the example below (click arrow to expand example).

<details>
<summary>Expand to see full example 👈</summary>

```typescript
import {
  type DispatchWithoutAction,
  type PropsWithChildren,
  createContext,
  useCallback,
  useContext,
  useEffect,
  useMemo,
  useReducer,
} from 'react'
import { createPortal } from 'react-dom'
import type { ToastOptions } from '@pluralsight/headless-styles/types'

const CLOSE_TOAST = 'close'
const SHOW_TOAST = 'show'

interface ToastState {
  show: boolean,
  heading?: string
  description: string
  onUndo?: () => {}
}

interface ToastAction {
  type: CLOSE_TOAST | SHOW_TOAST
  data: ToastState
}

function toastReducer(state: ToastState, action: ToastAction) {
  switch (action.type) {
    case SHOW_TOAST:
      return {
        ...state,
        ...action.data
      }

    case CLOSE_TOAST:
      return {
        ...state,
        sentiment: 'info',
        heading: '',
        description: '',
        onUndo: undefined,
        show: false
      }

    default:
      throw new Error('Something very bad has happened. This should never occur.')
  }
}

function showToastAction(dispatch: DispatchWithoutAction, data: ToastState) {
  return dispatch({
    type: SHOW_TOAST,
    data,
  })
}

function closeToastAction(dispatch: DispatchWithoutAction) {
  return dispatch({
    type: CLOSE_TOAST,
    data: initialToastState
  })
}

const initialToastState = {
  show: false,
  sentiment: 'info',
  heading: '',
  description: '',
  onUndo: undefined,
}

const ToastContext = createContext<ToastState>(initialToastState)

function ToastProvider(props: PropsWithChildren<{}>) {
  const [state, dispatch] = useReducer<ToastState>(reducer, initialToastState)

  const value = useMemo(
    () => ({
      state,
      dispatch
    }),
    [state, dispatch]
  )

  return (
    <ToastContext.Provider value={value}>
      {props.children}

      {state.show &&
        createPortal(
          <Toast
            onClose={() => closeToastAction(dispatch)}
            sentiment={state.sentiment}
            onUndo={state.onUndo}
          >
            <ToastHeading>{state.heading}</ToastHeading>
            <p>{state.description}</p>
          </Toast>
        , document.body)}
    </ToastContext.Provider>
  )
}

interface ToastHookOptions extends ToastOptions {
  heading?: string
  description: string
  onUndo?: () => {}
}

export function useToast() {
  const toastRef = useRef(null)
  const context = useContext(ToastContext)

  if (!context) {
    throw new Error(
      'useToast requires a ToastProvider to be used on a parent component.'
    )
  }

  const handleShow = useCallback((options: ToastState) => {
    showToastAction(dispatch, options)

    toastRef.current = setTimeout(() => {
      closeToastAction(dispatch)
    }, [9000])
  }, [dispatch])

  useEffect(() => {
    return () => clearTimeout(toastRef.current)
  }, [])

  return useMemo(() => ({
    close: closeToastAction(context.dispatch)
    show: handleShow
  }), [context, handleShow])
}
```

</details>

<br />

In this example, it should allow the developer to have an high-performant
experience like so (due to the use of useReducer):

In a parent/route component:

```typescript
<ToastProvider>
  <PageContent />
</ToastProvider>
```

In any child of that route component:

```typescript
const toast = useToast()

const handleUndo = useCallback(async () => {
  // reverse user's action
}, [])

async function handleClick() {
  try {
    // perform some server update..
    const update = await fetchThing()

    toast.show({
      sentiment: 'success',
      title: 'Channels updated',
      description: 'New channel bookmarked.'
      onUndo: handleUndo
    })
  } catch (e) {
    toast.show({
      sentiment: 'danger',
      title: 'Unable to update',
      description: 'The server failed to update.
    })
  }
}
```

With this design, the user only needs to:

1. Add a Provider
2. Use a hook that provides helper functions to show/hide a Toast notification without having to worry about managing a component or its state.

Additionally, we should just re-export the `useTheme` hook since this library would
make React-utils void for anyone using this library.

### Helper Functions

Along with all of the above, there should be some nice to have helpers that we ship
that help make Pando React even better.

I'm not totally for sure what these would be yet, but just noting.

What _shouldn't_ this be? We 100% should not be exporting 3rd party libs that
already do something. Each user can choose to add those to their specfiic project
if desired. Pando should always provide APIs that are original IPs.

### Requirements Still Needed

In order for this library to reach the ideal state, we would need to make the
following updates to the Pando ecosystem (this can be done in parrallel):

1. Headless-styles more than likely needs to refactor from CSS Modules to plain CSS in order to natively support Server Components (shipped in React 18 and used in Next 13)
2. Tabs hooks needs to be moved from React-utils to Pando React

## Drawbacks

### Why should we _not_ do this?

There are tradeoffs to choosing any path. Regarding implentation cost (both in
term of code size and complexity), there is not much there since we have all the
components already built out in the Headless-styles sandbox with only some fine-tuning
that should naturally be made for more robust situations. Likewise, we have had
a handfull of new people interested in helping contribute to a component library
which would also speed things up and reduce cost.

### Can this library be implemented in our users space? 

Yes, since most of our teams choose to use React, it should only help. However, 
it should be noted that this creates favoritism to React which is typically a negative 
point since engineering should never use tooling from a bias but instead as a 
result of what is the "best tool for the job". It is literally impossible that React
will always be the best tool for Pluralsight (and it may already be so, we are
just choosing not to see it?). By nature, one day this specific library will be obsolete.

### What would be the impact on teaching people about Pando React?

Minimal. The biggest hurdle will probably be teaching people about more
advanced/preferred React patterns based on performance and code scalability. 
This is only due to the larger amount of product code, 3rd party components, 
and libraries that exist in the world that do not use good React practices/anti-patterns 
(i.e. all in one components, etc.). However, I don't think this will really be that big of a deal?

### What about integration of this library with other existing features?

Everything in the [Pando Ecosystem](https://github.com/pluralsight/pando/tree/main/packages/headless-styles#what-role-does-this-package-play-in-pando) should naturally be able
to co-exists without requiring any additional work from the developer experience.
This also fulfills our [Commitment to Stability](https://design.pluralsight.com/docs/next/learn/about-pando/versioning#commitment-to-stability).

### What is the cost of migrating to Pando React from existing solutions (including Classic)?

By nature, Pando will be a breaking change since it is fundamentally different down
to the Web UI Kit (design options). However, it should allow the users to migrate
easier since it would mostly be updating prop names and values on already existing components.
Likewise, it should allow the users depenedency list to dramatically decrease since
they will only need a single package `@pluralsight/react` vs. a package for every single
component used and it's required dependecies (i.e. Classic, MUI, Chakra, etc.).

Ultimately, the pro's seem to outweigh the cons as long as users understand this
library is not meant to be the end-game to Pando (Headless-styles is). Headless-styles
will allow us to continue to scale throughout the trends of technology shifts by
nature of it being a vanilla Javascript library.

## Alternatives

We've attempted to use more advanced/current/modern designs via Headless-styles,
however, we have learned that ultimately, that design (at its current state) still
requires a high level of effort/work for the majority of Pando users. This is in
part due to the user needing to build (and own) their own components which in
some cases, have initiated feedback that a small segment felt uncomfortable with
this scenario.

Another alternative that was considered is using an already familiar 3rd party
under the hood (i.e. Chakra, MUI, etc.). Ultimately, this doesn't meet our need
and creates more issues regarding everything from maintaining code to managing
security issues which naturally stem from relying on a 3rd party.

In considering our original core value of Pando: creating a library that is
framework agnostic, web-components have been considered. However, this creates a
contradiction in going all in on components which is what we don't want to do. Likewise,
it creates overly-complexed code to maintain, just to be more marketable to the
world (i.e. we have all the component frameworks) when we can do the same thing
with a completely different design that isn't complex (i.e. Headless-styles).

## Adoption strategy

### If we implement this proposal, how will existing Pando developers adopt it?

Users will need to install the `@pluralsight/react` library and only use it in conjunction
with the Pando Normalize/Font setup [mentioned in the docs](https://design.pluralsight.com/docs/next/learn/get-started/installation/add-to-website). Users will _not_ need to install any other
Pando libraries to use the React one because it will use them internally.

### Is this a breaking change?

By nature of introducing a completely new design, yes, it will be a breaking change
but easier for most users to migrate than using Headless-styles due to having an
API that is familair.

### Can we write a codemod?

Could be worth exploring for _Classic users_. Classic by nature is not 1:1 to Pando
(and never will be) and is quite honestly very inconsistent in how it designs
components. However, it might work for the components that are used in the Pando
UI Kit.

There would need to be the following features:

1. Correct the import paths in each file
2. Correct the component usage and it's props in each file - without causing bugs
3. Delete unused Classic component code that is naturally built into Pando (there is a lot)
4. Delete unused Classic dependencies (see [migration list](https://design.pluralsight.com/docs/next/learn/learn-pando/migration)) from the users package.json
  a. Recognize whether a project uses Yarn/NPM and re-install everything automatically
5. Log warnings/errors for old Classic components that are no longer supported but are still around
  a. Probably a nice interface like "Pando no longer uses the Breadcrumb component, would you like to keep it? (Y/N)"

:warning: This should definitely be approached with caution! :warning:

If we can make this happen, the ROI on business **would be unlike anything
Pluralsight as a company has ever seen** regarding migrating to a new brand/tooling.

### Should we coordinate with other libraries within Pando?

Yes. All Pando libraries (i.e. and public package) should share the same version
number to make using and maintaining each package easier for the developer experience.

This should make it easier for a user to know if they are using compatible versions
if they choose to use multiple libraries within a single project (i.e. using both
`react` and `headless-styles`).

## How would we teach this?

The naming and terminology should be a direct mirror of the Headless-styles libray
which in turn mirrors the Design-Tokens. This helps create a consistent eco-system
that will scale throuhout time.

This will be presented in the Docs > Reference > [Component Name] page which would
replace the Headless-styles content with the component design. This update
would create the following change to the "story" of each reference page:

1. Intro
2. Anatomy
3. Import (component & types)
4. Usage
  a. Basic (component)
  b. Variants* (component)
5. Extending
  a. Classes (about using unused classes shipped with Headless-styles)
  b. Ejecting (using Headless-styles)
6. Reference (component)
  a. Props
  b. Caveats
7. Behavior
8. Accessibility

This story-flow will naturally answer the questions in the specific order:

1. What is this?
2. How do I get it?
3. How do I use it?
4. How do I customize it?
5. Where is the owner's manual?

By nature of components being a more familiar pattern, we would just shift how we teach
Pando to be component driven and only focusing on Headless-styles when we talk about
heavy customization that is not possible with the unused classes.

For existing Pando developers, they should learn that they have the option to use
whichever library (Headless-styles or React) is the best use case for their team.

## Unresolved questions

TBD