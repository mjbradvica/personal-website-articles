# Prefer Composition over Vue's V-Model

## The Problems

- Not very well supported via intellisense
- Violates SRP
- Goes against the ethos of one-way data flow
- Less to learn, standard event handling is uniform across Vue, React, and Angular
- Little or zero support for side-effects such as validation

## Use A Hook

We can get the same functionality out of a simple hook:

Define an interface:

```typescript
import type { Ref } from "vue";

export default interface IUseInput {
  text: Ref<string>;
  setValue: (event: Event) => void;
}
```

Then implement your interface via a custom hook:

```typescript
import { ref } from "vue";
import type IUseInput from "./i-use-input";

export default function useInput(initialValue = ""): IUseInput {
  const input = ref<string>(initialValue);

  const setValue = (event: Event): void => {
    input.value = (event.target as HTMLInputElement).value;
  };

  return { text: input, setValue };
}
```

## Conclusion

