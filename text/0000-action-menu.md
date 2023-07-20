- Start Date: 2023-07-19
- RFC PR: (leave this empty)
- Pando Issue: (leave this empty)

# Before Reading

Please approach this document with a generous perspective understanding that it is a Markdown file, so all examples are _theory_ and **have not been prototyped**. You may see "gotchas" which is normal.

If you choose to contribute, please address the overall purpose of the RFC and not nit-picking the "gotchas" in code examples. :heart:

# Summary

This RFC outlines the development of a headless ActionMenu equipped with extendable styling and enhanced accessibility. This improvement in the Pando platform's functionality could bring several noteworthy benefits:

## Purpose

1. **Improve Usability:** It provides a direct and straightforward way for users to navigate the application and perform tasks. It simplifies the user interface, improving the overall usability of the application.

2. **Consolidate Actions:** It consolidates various options or actions into a single, organized element, making it easier for users to find the functionality they need without having to navigate through different parts of the application.

3. **Streamline User Interactions:** An ActionMenu provides an organized structure where all related actions are grouped. This streamlining enhances the user's efficiency as they can quickly find the necessary action within a logically sorted list, resulting in a more intuitive user experience.

## Benefits
- **Modularity and Customizability:** The proposed headless ActionMenu promotes modular design and allows for extensive customization, fitting seamlessly into any design aesthetics.

- **Accessibility Enhancement:** The commitment to improve accessibility ensures that all Pando users, regardless of their abilities, can effortlessly interact with the platform, thereby promoting inclusivity.

- **Multi-Framework Support:** The headless ActionMenu's development will support various JavaScript frameworks, including React, Svelte, Angular, and Vue. This cross-framework compatibility ensures that developers working with any of these frameworks can seamlessly incorporate the ActionMenu into their projects.

- **Streamlining Development:** This feature introduces a standardized way to create and manage ActionMenus, reducing the time and resources needed for individual development tasks.

# Basic example

Here is a basic example using TailwindCSS for styles:

```jsx
<ActionMenu className="relative">
    <ActionMenu.Button className="text-black bg-white" aria-label="Open menu">
      Open menu
    </ActionMenu.Button>
    <ActionMenu.Overlay>
      <ActionList className="absolute bg-gray-700">
        <ActionList.Item onSelect={() => alert("Copy link clicked")}>
          Copy link
          <ActionList.TrailingVisual>⌘C</ActionList.TrailingVisual>
        </ActionList.Item>
        <!-- more items... -->
      </ActionList>
    </ActionMenu.Overlay>
</ActionMenu>
```
## Edge Cases
Here are a few potential corner-cases for this ActionMenu component:

1. **Empty ActionMenu**: What happens when an ActionMenu is rendered without any ActionList.Items? Your component should handle this case gracefully, possibly rendering an empty state message or not rendering the ActionMenu at all.

2. **A very long ActionList.Item**: What if one of your ActionList.Item's has a very long string or a complex node structure? The ActionMenu should handle this without breaking the layout or causing horizontal scrolling.

3. **ActionList.Item's onSelect function fails**: The onSelect function of ActionList.Item can potentially throw an error or not return. Your component should be resilient to these situations and provide proper error handling to ensure that the rest of your application isn't affected.

4. **High number of ActionList.Item's**: If the ActionMenu contains a very high number of items, it could potentially overflow off the page. The ActionMenu component should handle this condition, possibly by introducing scroll or limiting the number of items displayed at once and providing navigation to view additional items.

5. **Interactions with other components**: How does the ActionMenu behave if other components on the page have overlapping z-indexes, or if there's a modal/dialog open? How does it behave when nested inside another container or component with special properties (like overflow hidden)?

A ActionMenu component should be capable of handling a variety of content within its items, as well as a varying number of items.

# Motivation

The motivation for this feature is to provide a menu that is flexible in terms of styling, while also being accessible. It should integrate seamlessly with Pando's existing API, offer an easy-to-use interface for developers, and an intuitive and accessible experience for end users.

# Detailed design

The newly proposed `ActionMenu` embodies a headless component. This means it provides the functional logic of a menu without imposing any default styles. It is a composite of multiple sub-components, allowing it to adapt to and satisfy the requirements of any application. Moreover, it incorporates three paramount principles: accessibility (A11y), high performance, and ease of use.

1. **Accessibility (A11y)**: Prioritizing accessibility, the `ActionMenu` is designed to ensure that all users, regardless of their abilities, can smoothly interact with it. Every component part will incorporate accessibility standards as per the Web Content Accessibility Guidelines (WCAG) to ensure the platform's inclusivity.

2. **High Performance**: The `ActionMenu` is optimized for high performance. It's designed to be lightweight and responsive, ensuring fast loading times and a smooth user experience, irrespective of the application's scale.

3. **Ease of Use**: While offering complex functionality, the `ActionMenu` is designed with user-friendliness in mind. Developers can easily combine its components to create a custom ActionMenu tailored to their application's needs.

Here is a rundown of the `ActionMenu` structure:

- **`ActionMenu`**: This component acts as a wrapper for all other components, maintaining the state of the menu. Its design provides a flexible user interface that can be adapted to suit the unique requirements of any application, while preserving optimal accessibility.

- **`ActionMenu.Button`**: This component is the button that triggers the menu. Developers can define the appearance of the button, allowing for a high degree of visual customization while maintaining ease of interaction.

- **`ActionMenu.Overlay`**: This component encapsulates the `ActionList` and is the actual menu displayed when the `ActionMenu.Button` is clicked. It is designed with user accessibility in mind, ensuring that the menu is easily navigable.

- **`ActionList`**: This component acts as a wrapper for the menu items, allowing developers to customize the arrangement of items to suit their specific needs.

- **`ActionList.Item`**: This represents a single menu item. Each item is individually customizable, ensuring a balance between user interaction and accessibility.

The proposed design of the `ActionMenu` ensures that it is not only versatile and easy to use but also maintains an inclusive, accessible interface for all users while still being performant.

# Drawbacks

Creating an ActionMenu for a design system does come with its own set of potential drawbacks. Here are a few:

**Complexity and Resource Requirements**: Designing a flexible and widely usable ActionMenu can be a complex task, demanding substantial time and resources. Considering the diverse requirements of different applications, it's challenging to develop a solution that satisfies all possible needs.

**Increased Learning Curve**: An ActionMenu with advanced features and options may increase the learning curve for users. They may have to spend more time understanding how to implement and customize it effectively in their own applications.

**Potential Overhead**: If the ActionMenu is not implemented efficiently, it could introduce performance overhead in the applications where it's used. Poor performance may affect the user experience negatively.

**Integration Challenges**: There may be potential issues when integrating the ActionMenu with existing components or frameworks in the design system. These could range from styling conflicts to more substantial incompatibility issues.

**Maintenance Overhead**: As the design system evolves and new features are added, the ActionMenu will need to be maintained and updated regularly to ensure it remains consistent with the rest of the system. This could add additional overhead in terms of time and resources.

Despite these potential drawbacks, it's important to remember that well-designed ActionMenus can provide significant benefits in terms of usability and functionality in a design system. Thus, it's essential to weigh these drawbacks against the potential advantages.

# Alternatives

An alternative would be to create a standard menu component with predefined styles. This would reduce the complexity of the API but limit the ability of developers to customize the appearance of the menu.

## Downshift
Downshift is a popular library that allows developers to create custom dropdowns, comboboxes, and select boxes in a user-friendly manner. This library can serve as an alternative solution to create an ActionMenu.

It handles a lot of the complex interactions and accessibility concerns automatically, freeing developers to focus on the look, feel, and functionality of their ActionMenu. The biggest advantage is its headless nature, similar to the proposed ActionMenu, which offers the flexibility to style and structure the dropdown as desired.

Here's an example of how you might create an ActionMenu using Downshift:

```jsx
import { useSelect } from 'downshift'

function ActionMenu({ items }) {
  const {
    isOpen,
    selectedItem,
    getMenuProps,
    getItemProps,
    getToggleButtonProps,
  } = useSelect({ items })

  return (
    <div>
      <button {...getToggleButtonProps()}>Actions</button>

      <ul {...getMenuProps()}>
        {isOpen &&
          items.map((item, index) => (
            <li
              {...getItemProps({ item, index })}
              style={{ backgroundColor: selectedItem === item ? 'lightgray' : null }}
            >
              {item}
            </li>
          ))}
      </ul>
    </div>
  )
}

// Usage
const items = ['Edit', 'Delete', 'Copy']
<ActionMenu items={items} />
```

In this example, `useSelect` is a hook provided by Downshift that handles the logic of the dropdown, including opening and closing the menu and selecting items. You can provide it with a list of items, and it will give you back various props to apply to your components.

While Downshift provides a useful alternative, one must consider the trade-off in terms of having another dependency in the project. It could potentially introduce versioning issues and extra maintenance overhead. Therefore, it's essential to weigh these factors when choosing between a custom solution and a library like Downshift.

## Downshift drawbacks:
While Downshift is a powerful tool, there are several potential drawbacks to consider:

1. **Third-Party Dependency**: Adding Downshift as a dependency increases the complexity of your project's dependency tree. This means you're relying on an external team for updates and fixes. It might introduce potential versioning issues and dependency conflicts.

2. **Maintenance Overhead**: Over time, Downshift could introduce breaking changes, requiring you to dedicate time and resources to update your components and keep up with the library's evolution.

3. **Performance**: While Downshift is generally quite performant, it's another library added to your bundle size, which could potentially impact your application's load times and overall performance.

4. **Customization Limitations**: Despite its headless nature and flexibility, there might still be limitations in customizing the behaviors of Downshift. Some very specific requirements might not be achievable using the library.

5. **Learning Curve**: While Downshift simplifies many complex aspects of creating dropdown menus, it still has its learning curve. Developers need to understand the Downshift's way of doing things, which could take time and possibly slow down the development process initially.

These potential drawbacks need to be weighed against the benefits of faster development and pre-handled accessibility and UX concerns. The decision should be based on the specific needs and constraints of your project.

Sure, here is an expanded adoption strategy:

# Adoption Strategy

The introduction of the `ActionMenu` feature into the Pando library should be managed carefully to ensure a smooth transition for developers and to maximize its utilization. It is proposed to use the following strategy:

1. **Documentation and Examples**: Thorough documentation should be provided. This includes its structure, the different components it comprises, and how they interact. Real-world examples demonstrating different use-cases and implementations should also be provided to aid in understanding and application.

2. **Educational Resources**: Consider creating educational resources such as tutorials or webinars to walk developers through its use. This can help reduce the learning curve and increase its adoption.

3. **Internal Promotion**: Encourage its adoption internally within the Pando ecosystem. Highlight its benefits and use-cases in developer meetings or internal newsletters to raise awareness and promote its use.

4. **Incremental Adoption**: Encourage developers to start incorporating the `ActionMenu` in their new features or when updating existing ones. This gradual approach will ensure that the transition to the new feature does not disrupt ongoing projects.

Remember, the main goal is to make the adoption process as seamless and beneficial as possible for the developers. Regularly soliciting and addressing feedback can help identify and resolve any barriers to adoption.

# How We Teach This

Implementing the `ActionMenu` into the Pando library requires a well-crafted educational strategy:

1. **Documentation**: Create in-depth, easy-to-understand documentation detailing the individual components of the `ActionMenu` and their interactions.

2. **Use-case Examples**: Present examples of `ActionMenu` applications for users to understand its practical implementation across varying complexity levels.

3. **Guides and Tutorials**: Develop concise step-by-step guides or tutorials, taking developers through creating a custom menu.

4. **Interactive Learning**: Establish a sandbox environment where users can experiment to gain hands-on experience.

5. **Community Engagement**: Foster a Pando community culture where users can share their implementations and discuss best practices.

6. **Continued Support**: Maintain channels for continuous support, like a dedicated forum, regular Q&A sessions, or updates to the documentation based on user feedback.

This strategy is designed to support users in leveraging the full potential of the `ActionMenu`, aligning with Pando's principles of flexibility and accessibility.


# Unresolved questions

As we progress with the ActionMenu design and implementation, several areas need further exploration. Key considerations include ensuring accessibility compliance, optimizing performance, integrating with existing Pando features, managing adoption and migration, and considering third-party dependencies like Downshift. Post-release user feedback will also be essential to refine and improve the ActionMenu. These issues form an evolving dialogue that will guide our next steps and decisions.