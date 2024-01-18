# Mimicking useState in Vue

## The What

useState is the most basic hook in React. It allows you to store a variable and your application will re-render when said variable is updated.

Vue has the same concept with Ref's. They serve the same purpose but are implemented differently.

## The Why?

Ref's have a few disadvantages compared to useState:

1. Generic Ref's require a cast back to a type of Ref. (This is a TypeScript restriction)
2. It can be confusing having to dereference state with the ".value" property for both getters and setters
3. You are unable to enforce state initialization

We can solve all three of these issues with a very simple composable.

## The How

```typescript
import { ref, Ref } from "vue";

export default function useState<T>(
  initial: T,
): [state: Ref<T>, setState: (newState: T) => void] {
  const currentState = ref<T>(initial) as Ref<T>;

  const setState = (newState: T): void => {
    currentState.value = newState;
  };

  return [currentState, setState];
}
```

And we can use it like a normal useState hook.

```typescript
<script setup lang="ts">
const [name, setName] = useState<string>("jimmy");
</script>
```

With our new composable, we no longer need to worry about any generic state casts. We only dereference state for getters, and state initialization is always enforced. This composable gives us three quality of life fixes that can greatly help us for the duration of our applications' lifecycle.
