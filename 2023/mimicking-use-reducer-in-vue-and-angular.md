# Mimicking useReducer in Vue & Angular

## A Common State Problem

The useReducer hook exists to solve a common problem in front-end development. Having to drill state and callbacks through three to five components. Using normal state variables and callbacks would lead to multiple bloated component props; but using global state would be overkill.

This is where the useReducer hooks excels. When we need that goldilocks state solution of requiring more than local state, but less than global state.

## It's Just A Switch Statement

The useReducer hooks comes in three parts:

- The hook itself--a wrapper around some state and a function to manipulate said state.
- A reducer function that will return a new state object given our current state and some action.
- A set of actions which are typically modeled as an enum.

Another way to think of this hook is the [Command Pattern](https://en.wikipedia.org/wiki/Command_pattern). We are taking n number of callbacks and condensing them into an object or enum.

## Creating our Hooks

Sadly, unlike React, Vue and Angular do not come with a reducer hook out of the box. However, it is very simple to re-create these powerful tools.

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
import { Signal, signal } from "@angular/core";

export default function useReducer<T, K>(
  reducer: (currentState: T, action: K) => T,
  defaultState: T
): [Signal<T>, (action: K) => void] {
  const state = signal<T>(defaultState);

  const dispatcher = (action: K): void => {
    state.set(reducer(state(), action));
  };

  return [state, dispatcher];
}
```

Utilizing a hook is as easy as creating a reducer function and then initializing the hook where needed.

```typescript
export enum MyAction {
  First = 0,
  Second = 1,
  Third = 3,
}
```

```typescript
export default function myReducer(state: MyState, action: IAction): MyState {
  switch(action) {
    case MyAction.FirstCase {
      return updatedStateObject;
    }
    // more switch cases here...
  }
}
```

In our component, we only need to pass the state object and dispatcher function to all of our child components.

```typescript
<template>
  <MyChildComponent :state="state" :dispatcher="dispatcher" />
</template>

<script setup lang="ts">
import { useReducer } from "./UseReducer";

const [state, dispatcher] = useReducer(myReducerFunc, defaultState);
</script>
```

## Conclusion

If you need to pass a payload to our reducer, simply update your actions to be objects that encase the action type and the payload. Cast the payload to the type needed in your reducer function, then use as needed.

The useReducer hook is a great standard feature in React that is easy to implement in Vue and Angular as well. It solves the common problem of how to handle state in intermediate settings where local and global state are ill suited for the task.
