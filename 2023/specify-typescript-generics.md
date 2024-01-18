# Specify TypeScript Generics

Generics are implemented differently in each programming language. This is just the reality. C++, C#, and Java all have different rules and implementations for generics--TypeScript is no different.

TypeScript generics do not infer generics consistently like normal normal static languages.

Take a look at the following Vue computed function:

```typescript
const result = computed(() => {
  if (hook.valid) {
    return "valid";
  }
  return "";
});
```

What do you thing the return type is?

You may think it is a ComputedRef of type string, but the type is actually:

```typescript
ComputedRef<"" | "valid">;
```

The end result is the same, but this is a [leaky abstraction](https://en.wikipedia.org/wiki/Leaky_abstraction). Our function should not leak the details of the _exact_ return type. We want our function to return a string.

The only thing we need to change is:

```typescript
const result = computed<string>(() => {
  if (hook.valid) {
    return "valid";
  }
  return "";
});
```

And, now, our return type is fixed.

Specifying every generic in your application is likely excessive.Some functions are able to infer your return types with accuracy--others not so much. Keeping your return types in check will help contribute to a rock solid code base.
