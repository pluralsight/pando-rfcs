- Start Date: 2023-1-11
- RFC PR: (leave this empty)
- Pando Issue: (leave this empty)

# React-Utils: Prop Merge Utility

## Summary

Merge props returned by our helpers and hooks so they can be applied to the element without conflicts or additional effort.

## Basic example

The submenu combines multiple hooks, which have some conflicts when it comes to their returned props.  Using a utility to merge the props results in cleaner, more reliable code.

### Without the utility

```jsx
function Submenu(props) {
  const { onBlur: onTriggerBlurTabIndex, ...rovingTabIndexProps } =
    useRovingTabIndex()
  const { expanded, menu, trigger } = useSubmenuInteraction()
  const { onBlur: onTriggerBlurInteraction, ...triggerInteractionProps } =
    trigger
  const submenuStyles = getMenuProps({
    label: props.label,
  })
  const listItem = getMenuItemProps()

  function handleTriggerBlur(e) {
    onTriggerBlurInteraction(e)
    onTriggerBlurTabIndex(e)
  }

  return (
    <li {...listItem.menuListItem}>
      <button
        {...listItem.menuItem}
        {...triggerInteractionProps}
        {...rovingTabIndexProps}
        onBlur={handleTriggerBlur}
      >
        <p {...listItem.menuItemText}>{props.label}</p>
        <ChevronRightIcon {...getIconProps(submenuStyles.iconOptions)} />
      </button>

      {expanded && (
        <menu {...submenuStyles.menu} {...menu}>
          {props.children}
        </menu>
      )}
    </li>
  )
}
```

### With the utility

```jsx
function Submenu(props) {
  const submenuStyles = getMenuProps({
    label: props.label,
  })
  const menuItemStyles = getMenuItemProps()
  const rovingTabIndexProps = useRovingTabIndex()
  const submenuInteractionProps = useSubmenuInteraction()

  return (
    <li {...menuItemStyles.menuListItem}>
      <button
        {...mergeProps(
          menuItemStyles.menuItem,
          submenuInteractionProps.trigger,
          rovingTabIndexProps
        )}
      >
        <p {...menuItemStyles.menuItemText}>{props.label}</p>
        <ChevronRightIcon {...getIconProps(submenuStyles.iconOptions)} />
      </button>

      {expanded && (
        <menu {...mergeProps(submenuStyles.menu, submenuInteractionProps.menu)}>
          {props.children}
        </menu>
      )}
    </li>
  )
}
```

## Motivation

> Why are we doing this? What use cases does it support? What is the expected
> outcome?
>
> Please focus on explaining the motivation so that if this RFC is not accepted,
> the motivation could be used to develop alternative solutions. In other words,
> enumerate the constraints you are trying to solve without coupling them too
> closely to the solution you have in mind.

Combining small, targeted functions gives us great power and flexibility, and are cleaner and easier to maintain.  However, it also means that we may have multiple helpers returning the same properties for the same element.

We want to be able to safely and consistently combine returned props so that they can be applied without requiring any additional manipulation.

We want to reduce the impact of future additions.  For example, if an API adds a prop to its output that now conflicts with another, if they were merged with this utility then bugs should be avoided.

We want to improve the flexibility of our helpers, allowing more composition and reuse with other libraries, not just our own.

## Detailed design

The utility should accept 2 or more objects and merge them according to the following rules:

- Skip inherited properties; only merge 'own' properties
- **`class`, `className`, `aria-labelledby`, `aria-describedby`:** Concatenate with single-space separators
- **Event handlers (`onChange`, etc):** Wrap in function, calling in order received in argument list
- **`style`:** Shallow merge
- **`ref`:** TBD
- **Other string, boolean, number:** Direct assignment. overwrite with latest value
- **Other Objects:** Recurse through prop merge

Errors should be thrown for any props where the merging strategy is unclear and could create undesireable effects:

- More than 1 ID

Examples

```javascript
const props1 = {
  expanded: false,
  trigger: {
    id: 'menu-1:trigger',
    onBlur: handleBlur1,
    'aria-labelledby': 'label1Id',
    className: 'menuTrigger',
    tabIndex: 0,
    style: {
      borderRadius: '5px',
      backgroundColor: 'var(--ps-action-background)',
    }
  }
}

const props2 = {
  trigger: {
    onBlur: handleBlur2,
    'aria-labelledby': 'label2Id'
    className: 'menuItem',
    tabIndex: -1,
    style: {
      backgroundColor: 'inherit'
    }
  }
}

const mergedProps = mergeProps(props1, props2)

/*
mergedProps = {
  expanded: false,
  trigger: {
    id: 'menu-1:trigger',
    onBlur: function(e) {
      handleBlur1(e)
      handleBlur2(e)
    },
    'aria-labelledby': 'label1Id label2Id',
    className: 'menuTrigger menuItem',
    tabIndex: -1,
    style: {
      borderRadius: '5px',
      backgroundColor: 'inherit',
    }
  }
}
*/
```

## Drawbacks

Why should we *not* do this? Please consider:

- implementation cost, both in term of code size and complexity
- whether the proposed feature can be implemented in user space
- the impact on teaching people a Pando library
- integration of this feature with other existing and planned features
- cost of migrating existing Pando applications (is it a breaking change?)

There are tradeoffs to choosing any path. Attempt to identify them here.

## Alternatives

> What other designs have been considered?

Leveraging a 3rd party solution:

- [Adobe React Aria > mergeProps](https://react-spectrum.adobe.com/react-aria/mergeProps.html)
- [merge-props](https://github.com/andrewbranch/merge-props)

The delivery of the helper - whether it is exported directly or used by other helpers to merge incoming props with their output.

> What is the impact of not doing this?

The impact would be requiring users to merge props themselves, resulting in additional effort, complexity, and reduced consistency.

## Adoption strategy

> If we implement this proposal, how will existing Pando developers adopt it? Is
> this a breaking change? Can we write a codemod? Should we coordinate with
> other projects or libraries?

- Use in documentation and sandbox examples

## How we teach this

> What names and terminology work best for these concepts and why? How is this
> idea best presented? As a continuation of existing Pando patterns?
>
> Would the acceptance of this proposal mean the Pando documentation must be
> re-organized or altered? Does it change how Pando is taught to new developers
> at any level?
>
> How should this feature be taught to existing Pando developers?

- Documentation and other tutorials/support

## Unresolved questions

- How best to merge React refs?
- Any other special cases missing?
- Support for other formats beyond React/JSX?
- Should this be a seperate utility (as presented), or integrated into primary prop getters? (Strongly leading toward the former, but still open to exploring other options)
