# Mimicking useReducer in Vue & Angular

## It's Just A Switch Statement

Vue

```typescript
import type { Ref, ref } from "vue";

export default function useReducer<T, K>(
  reducer: (currentState: T, action: K) => T,
  defaultState: T
): [Ref<T>, (action: K) => void] {
  const state = ref<T>(defaultState) as Ref<T>;

  const dispatcher = (action: K): void => {
    state.value = reducer(state.value, action));
  };

  return [state, dispatcher];
}
```

Angular

```typescript

```
