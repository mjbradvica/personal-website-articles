# Navigating Vue's onMounted ambiguity

## Understanding the onMounted hook

If you come from a React background, you may already be aware of [the rule](https://www.robinwieruch.de/react-hooks-fetch-data/) that state you should avoid async callbacks in useEffect hooks. This is because the return type of said hook is not supposed to be a Promise. In Vue, the onMounted hook serves some of the same purposes as useEffect in React, but it is implemented differently. In Vue, the [onMounted hook](https://vuejs.org/api/composition-api-lifecycle.html#onmounted) is called ***after*** the component is has been mounted. This means it does not required promises to resolve in order to render.

So while this works:

```typescript
onMounted(async () => {
    // await something in here
});
```

The documentation says otherwise though:

```typescript
function onMounted(callback: () => void): void
```

> Return type of void is not the same as a Promise of type void

If we try to peek into the actual code, we see this--even for TypeScript.

```typescript
export declare const onMounted: (hook: () => any, target?: ComponentInternalInstance | null) => false | Function | undefined;
```

This leads to the ambiguity of how this hook should or should not be implemented. This may be a small issue, but it may present itself as a pain point with fellow developers who may see things one way or the other.

## Using a custom hook instead

Instead of fussing about how our fellow developers should or should not use the onMounted hook--it is best to provide them with a composable so they never need to think about it.

### useMountedFetch

```typescript
import { onMounted, Ref, ref } from "vue";

export default function useMountedFetch<T>(initial: T, callback: () => Promise<void>): Ref<T> {
  const state = ref<T>(initial) as Ref<T>;

  onMounted(() => {
    const fetchData = async (): Promise<void> => {
      const result = await callback();

      state.value = result;
    };

    void fetchData();
  });

  return state;
}
```

Then use the composable as follows:

```typescript
const myData = useMountedFetch("", async () => {
    // fetch or axios call in here
});
```

> I recommend not inlining your data calls and moving them to a separate file.

This will fulfill the needs of any data fetching that needs to be done when a component renders. Providing our fellow developers with a composable takes the decision out of their hands. We also enjoy the niceties of avoiding issues with Ref's. The use of a custom composable here leads to a more consistent and predictable codebase.
