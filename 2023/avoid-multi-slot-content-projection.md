# Avoid Multi-Slot Content Projection

Content projection in React, Vue, and Angular follows a familiar pattern. A parent component allows for an unknown child to be rendered inside a section of the component. This allows for the parent to act as an anonymous wrapper.

The important word here is _anonymous_. The key to this relationship is the parent has no knowledge of the child, and the child has no knowledge of the parent.

> Because content projection allows you to pass anything, encapsulation of component internals _must_ be preserved.

## Slots are an Open Contract

When you allocate a component to accept children to be projected in React, Angular, or Vue, you are defining either a required or optional area for N number of components to be rendered in that slot.

Named slots break this contract because the developer has to specify what slot is to be used, breaking the encapsulation of the parent component.

Multi-Slots in Vue:

```typescript
<template>
  <slot name="primary"></slot>
  <slot name="secondary"></slot>
</template>
```

The children can use the parent as so:

```typescript
<template>
  <template #primary>
    <p>Inside primary.</p>
  </template>
  <template #secondary>
    <p>Inside secondary.</p>
  </template>
</template>
```

The encapsulation of the parent component is broken with multiple slots. The child is now aware of the internals of the parent. A consequence of this internal knowledge is that all child components have a hard dependency on the parents' slot names. If a slot is changed in the parent, every child will need to be updated.

## Other Content Projection Fallacies

Named slots are not the only abused feature of content projection. Vue allows you to dynamically assign names to slots--you can even pass props! These are fatally flawed features offered by Vue that should be avoided.

With dynamic slots, you are able to name and change the name of a slot at runtime.

```typescript
<template #[mySlotName]>
    // Content in here
</template>
```

Just like before, you have a hard dependency between a parent and child component where one should not exist. This one has the potential for even more errors because (unlike the previous code example, which was static) this can change-leading to a higher chance for bugs in your software.

Vue also allows you to pass props to slots. This is also completely unnecessary. If you find yourself passing props to a slot, it is important to step back from your work and analyze the problem you are attempting to solve.

> Any problem can be solved in modern front-end development via composition, higher-order components, callbacks, and normal props.

If you are attempting to pass props to a slot in a TypeScript project, you are breaking the ethos of TypeScript--a language whose purpose revolves around hard types and Intellisense. Because the child in the slot may be any component, you are not guaranteed a matching interface. Passing too many props breaks the principle of Interface Segregation.

## Boring Is Beautiful

If you are ever tempted by the allure of either multi-slot content projection, dynamic slot names, or passing props to slots, fall back on these options first.

1. Re-think your component organization and flow
2. A higher-order component
3. Breaking up a complex component into smaller ones

Content projection in all front-end frameworks is an _explicit_ contract between a parent and a single child. Holding firm to this concept will produce better software that is much easier to maintain in the long-term.
