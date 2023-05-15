# Avoid Satisfies in TypeScript

## New keyword in TS 4.9

```typescript
interface Person {
    name: string;
    age: number;
}

const Daddy = {
    name: string;
    age: number;
    isMarried: boolean;
} satisfies Person;
```

## Stick to extending
