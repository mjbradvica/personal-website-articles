# Avoid Vue Component Events

## Overview

Component events in Vue allow you to emit a customer event to the parent.

```typescript
<button @click=$emit('myEvent')>Im A Button</button>
```

You can then listen to said event from a parent component.

```typescript
<MyComponent @some-event="callback" />
```

This is an unnecessary feature in Vue that should be avoided entirely.

## Issues

Compared to a callback which gives you:

1. Built in type safety
2. No strings to deal with
3. No boilerplate to define emits
4. A uniform convention with other front-end frameworks

### Emits have no built in type safety

Callbacks in Vue are type safety because you define the delegate in the component properties.

```typescript
<script setup lang="ts">
export interface MyComponentProperties {
    myCallback(): => void;
}

const properties = defineProps<MyComponentProperties>();
</script>
```

Components events are by default not type safe. They offer you the ability to add in type safety like so.

```typescript
<script setup lang="ts">
const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()
</script>
```

There is no need to do this if you can accomplish the same thing in your properties definition file.

### Emits require strings

When you use a component event, you need to define the event with a name. You need to either hard code a string or use a separate constant.

```typescript
<button @click=$emit('myEvent')>Im A Button</button>
```

This is extra busy work when a callback requires none of this:

```typescript
<button @click="handleClick">Im A Button</button>
```

### Emits require boilerplate definitions

Using a component event means you need to annotate your components with a definition for each event you intend to emit.

```typescript
<script setup>
defineEmits(['inFocus', 'submit'])
</script>
```

Callbacks are again, defined in your properties interface and do not require such configuration.

### Emits are not uniform

You handle events in React with callbacks. You handle events in Angular with callbacks. You should handle events in Vue with callbacks as well. All of these frameworks are functional by nature. Use functions.

## Conclusion

Component events in Vue are a useless feature. They offer a solution for a problem that does not exists. They increase your line count while reducing code quality. Use normal callbacks passed via properties.
