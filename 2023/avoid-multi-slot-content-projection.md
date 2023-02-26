# Avoid Multi-Slot Content Projection

Content projection in React, Vue, and Angular follows a familiar pattern. A parent component allows for an unknown child to be rendered inside a section of the component. This allows for the parent to act as an anonymous wrapper.

The important word here is "anonymous". The key to this relationship is the parent has no knowledge of the child and the child has no knowledge of its parent. 

> Because content project allows you to pass anything--encapsulation of component internals *must* be preserved.

## Slots Are An Open Contract

When you allocate a component to accept children to be projected in React, Angular, or Vue, you are defining either a required or optional area for N number of components to be rendered in that slot.

Named slots break this contract because the developer has to specify what slot is to be used-hence breaking the encapsulation of the parent component.

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

With multiple slots our encapsulation of the parent component has been broken. The child is now aware of the internals of the parent. A consequence of this internal knowledge is that all child components have a dependency on the parents' slot names. If a slot is changed in the parent, every child will need to be updated.

## Props Allow For More Defined Components

The first alternative for using multiple slots should be to use standard props instead. 

## content projections allows you to pass anything...

> Don't be cute with content projection. It should be used as an independent wrapper where both the child and parent component have zero knowledge about the internals of the other.

## don't pass props to slots, when you can just pass props to a component

## can't enforce required children

## sign your component is doing too much

## this is what props is for