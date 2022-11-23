# Always Initialize State

When using state objects in React or Vue you may provide a default value for each variable.

This appears so:

```typescript
const [state, setState] = useState<number>(10);
```

or in Vue:

```typescript
const state = ref<number>(10);
```

> Even though it's optional, you should always initialize your state objects.

State objects that are not initialized will default to undefined. The type will be the generic you specify, or undefined. If you refrain from giving the generic a type, it will be of type 'any'. None of this is desired in front-end development.

We want to avoid undefined as much as possible.

Undefined variables leave you open to runtime errors when de-referencing objects. It forces you to insert guards to check for possible undefined variables in code. Leaving the option open for undefined variables is inviting bad things to happen.

## Initializing State Variables

We have two kinds of variables to initialize-primitive and reference types.

Primitive types are easy; you can have an object that represents default types:

```typescript
const ValueDefaults = {
  Number: 0,
  Boolean: false,
  String: "",
};
```

You can use them in your code as follows:

```typescript
const [state, setState] = useState<number>(ValueDefaults.Number);
```

Declarative variables are ideal for teams. Ultimately, they allow us to be explicit with our intentions. I highly suggest you enable the ESLint [no magic numbers](https://typescript-eslint.io/rules/no-magic-numbers/) to help enforce this rule.

Since arrays are reference types, you need to provide a blank array:

```typescript
const people = ref<Array<number>>([]);
```

With objects I recommend a function that returns a blank object as a placeholder...

```typescript
export interface PersonResponse {
  id: string;
  name: string;
  age: number;
}

export function defaultPersonResponse(): PersonResponse {
  return {
    id: ValueDefaults.String,
    name: ValueDefaults.String,
    age: ValueDefaults.Number,
  };
}
```

which can be used like so:

```typescript
const admin = ref<PersonResponse>(defaultPersonResponse());
```

Initializing state variables is one of the easiest things you can do to reduce the possibility of bugs in code. This concept is the same as the [Null Object Pattern](https://en.wikipedia.org/wiki/Null_object_pattern). Default state variable help to declare the intent of your code base and prevent unnecessary errors.
