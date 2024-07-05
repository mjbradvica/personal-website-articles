# Simplifying State Updates for Reference Types

## The Problem

If you are working in React, Vue, or Angular you've probably had issues with state objects updating and re-rendering properly. This is because reference types in JavaScript/TypeScript must be deep-cloned properly to ensure that framework state hooks can pick up changes properly.

```typescript
const [state, setState] = useState<MyObject>();

state.property = "newValue";

setState(state);
```

The following code snippet does not do what you think it does. Because the "state" object is the same reference, the state hook or "ref" in Vue is unable to detect the change in the property.

The correct way to accomplish this state update is to perform a full deep clone of the state object.

```typescript
const [state, setState] = useState<MyObject>();

const newState = { ...state };
newState.property = "newValue";

setState(newState);
```

However, the frustration with this method is magnified when you have state objects with multiple levels that must all be deep-cloned individually. It leads to a serious boilerplate issue that pollutes code bases. Many developers choose to rectify this by serializing and deserializing the object, however, this is a band-aid over a bigger issue.

```typescript
const [state, setState] = useState<ComplicatedObject>();

const newState = JSON.parse(JSON.stringify(state)) as ComplicatedObject;
newState.property.subProperty = "newValue";

setState(newState);
```

This method works, but it is ugly as it requires a sub-optimal way of producing a deep clone that must be hard-casted to the correct type.

## Solution

There is a built-in method in Node that natively provides deep clone capability without the code smells. We can wrap this method in a custom hook to give us consistent state updates without future headaches.

```typescript
import { useState } from "react";

export default function useDeepState<T>(
  initialState: T
): [T, (newState: T) => void] {
  const [state, setState] = useState<T>(initialState);

  const updateState = (newState: T): void => {
    setState(structuredClone<T>(newState));
  };

  return [state, updateState];
}
```

If you are using Vue...

```typescript
import { Ref, ref } from "vue";

export default function useDeepState<T>(
  initialState: T
): [Ref<T>, (newState: T) => void] {
  const state = ref<T>(initialState) as Ref<T>;

  const setState = (newState: T): void => {
    state.value = structuredClone<T>(newState);
  };

  return [state, setState];
}
```

The [StructuredClone](https://developer.mozilla.org/en-US/docs/Web/API/structuredClone) method is useable in most modern front-end frameworks to give developers a type-safe, native implementation of the deep clone algorithm. We have simply wrapped the method in a new hook that may be reused in our application.

> This new hook is only required for reference types. Primitives need not apply.

With this new implementation, you may update any state reference type by simply changing the property or field. No more custom cloning, JSON workarounds, or frustration.
