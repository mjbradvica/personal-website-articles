# Use Value Defaults

## Declarative Variables

Value Defaults for front-end applications are very simple, a file with default values for primitive types and arrays.

### ValueDefaults.ts

```typescript
const ValueDefaults = {
  String: '',
  Number: 0,
  Boolean: false,
  Date: Date.UTC,
  emptyArray: <T>() => new Array<T>(),
};

export default ValueDefaults;
```

This allows you to be absolutely explicit with value that you are initializing in your application.

```typescript
const myVariable = ref<string>('');
```

versus...

```typescript
const myVariable = ref<string>(ValueDefaults.String);
```

The first is illusive. Did you really mean to use an empty string-or did you just forget to initialize with a value? The second example is absolute. You want an empty string, and it is clear to other developers as well.

## Pair with No Magic Numbers

Using this small file in conjunction with the ESLint rule [No Magic Numbers](https://typescript-eslint.io/rules/no-magic-numbers/) will result in high value for your application with almost little effort. This rule is not included in the default config, so you will need to turn it on yourself.

Small changes like this to your application help contribute to a strong code base.