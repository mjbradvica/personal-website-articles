# Specify TypeScript Generics

The reality of programming languages is that generics are implemented different in each language. C++, C#, and Java all have different rules and implementations for generics--TypeScript is no different.

One of the rules with TypeScript generics is that the type inference is not consistent.

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

You think it would be a ComputedRef of type string, but the type is actually:

```typescript
ComputedRef<"" | "valid">
```

The end result is the same, but this is a [leaky abstraction](https://en.wikipedia.org/wiki/Leaky_abstraction). Our function should not leak the details of the *exact* return type. We want our function to return a string.

The only thing we need to change is:

```typescript
const result = computed<string>(() => {
  if (hook.valid) {
    return "valid";
  }
  return "";
});
```

And now our return type is fixed.

Specifying every generic in your application is potentially overboard. Some functions are able to correctly infer the your return types--others not so much. Keeping your return types in check will help contribute to a rock solid code base.