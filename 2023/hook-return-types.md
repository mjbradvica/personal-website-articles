# Hook Return Types

## The 3, 2, 1 Rule

We have three options for returning a hook in React, Vue, or Angular. We can choose between:

- A single variable
- Destructured array
- Destructured object or proper interface

We just need to keep the 3, 2, 1 rule in mind for knowing what we should return.

If we are returning just a single state object, only the variable is required. No need for it to be returned inside an array or object.

```typescript
export default useMyHook(): string {
    const partA = useState<string>();

    // Other methods and lifecycle method's here.

    return partA;
};

const myHookObject = useMyHook();
```

For two or three variables, an array is the best choice.

```typescript
export default useMyHook(): [string, number] {
    const partA = useState<string>();
    const partB = useState<number>();

    // Other methods and lifecycle method's here.

    return [partA, partB];
};

const [partA, partB] = useMyHook();
```

Finally, with three variables, we can still return an array. But anything that returns four or more should always be an object.

```typescript
export default useMyHook(): { partA: string, partB: number, partC: Date } {
    // Ref's and other lifecycle methods here.

    return { partA, partB, partC }
};

const myHookObject = useMyHook();
```

Sometimes if the object is large enough it may be a good idea to define an interface to avoid a confusing return type.

```typescript
export default interface IMyHook {
  partA: string;
  partB: number;
  partC: Date;
  partD: boolean;
}

export default function useMyHook(): IMyHook {
  // Ref's and other lifecycle methods here.

  return { partA, partB, partC, partD };
}
```

## Vue Is Complicated

Because of how [Vue's reactivity works](https://vuejs.org/guide/reusability/composables.html#conventions-and-best-practices). We need to return naked Ref's from our hooks. While the following is possible:

```typescript
const myHook = useMyHook();

const [partA, partB] = useMyHook();

const { partA, partB, partC } = useMyHook();
```

The following is not allowed by default.

```typescript
export default useMyHook(): { partA: string, partB: number, partC: Date } {
    // Ref's and other lifecycle methods here.

    return { partA, partB, partC }
};

const myHookObject = useMyHook();
```

Returning an object of Ref's will cause the hook to lose it's reactivity. We need to destructure the object like shown above.

```typescript
const { partA, partB, partC } = useMyHook();
```

### There Is A Way

You can get around this by wrapping your hook in a "reactive" function that will restore the reactivity to your hook.

```typescript
const myHookObject = reactive(useMyHook());
```

However, I do not recommend doing this, as it may be confusing to other developers when and where this is needed.

## Conclusion

If you stick to the 3, 2, 1 rule, you'll provide a consistent interface to your fellow developers on how hooks can and should be interacted with. Just remember to keep your Vue hooks destructured.
