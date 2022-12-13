# Mimicking useComputed in React

## Background

One of the best features in Vue is the [useComputed](https://vuejs.org/guide/essentials/computed.html#basic-example) composable. It allows use to create a pure function that reacts in accordance to the callback passed to it.

One use of the useComputed composable is to generate a last name from a first and last:

```typescript
const first = ref<string>("");
const last = ref<string>("");

const lastName = computed<string>(() => first + last);
```

Here is how we would normally concatenate two names in React:

```typescript
const [first, setFirst] = useState<string>("");
const [last, setLast] = useState<string>("");

const [full, setFull] = useState<string>("");

useEffect(() => {
    setFull(first + last);
}, [first, last]);
```

In React, we need to create a state variable, then update this variable via a useEffect hook. In many cases, this is necessary if we must update the variable at a later time. Since this use case is a pure function, we need only the result of the function. Creating our own version of the computed function would allow us to inline our code and keep the result pure.

## useComputed Hook

Here is our hook:

```typescript
import { useEffect, useState } from "react";

export default function useComputed<T>(initialState: T, callback: () => T): T {
  const [state, setState] = useState<T>(initialState);

  useEffect(() => {
    setState(callback);
  }, [callback]);

  return state;
}
```

And here is how we use it:

```typescript
  const [first, setFirst] = useState<string>("");
  const [last, setLast] = useState<string>("");
  
  const fullName = useComputed(
    "",
    useCallback(() => first + last, [first, last])
  );
```

Unlike our Vue counterpart we need to have a few extras:

- Our state variable needs to be provided with an initial value.
- Our function *must* be wrapped in a useCallback; if we omit this it will cause an infinite loop.

Our end result allows us to remove some of the ceremony around create two separate hooks and wiring them together. As long as we remember to wrap our function in a useCallback hook, the result is a net positive.
