# Use Value Defaults

## Declarative Variables

Value Defaults for front-end applications are very simple. A file with default values for primitive types and arrays.

### ValueDefaults.ts

```typescript
const ValueDefaults = {
  String: '',
  Number: 0,
  Boolean: false,
  Date: new Date(),
  emptyArray: <T>(): Array<T> => new Array<T>(),
};

export default ValueDefaults;
```

This allows us to be absolutely explicit with the value that you are initializing in your application.

```typescript
const myVariable = ref<string>('');
```

versus...

```typescript
const myVariable = ref<string>(ValueDefaults.String);
```

The first is elusive. Did you really mean to use an empty string, or did you just forget to initialize with a value? The second example is absolute. Other developers on your project clearly understand your intent.

## Pair with No Magic Numbers

Using this small file in conjunction with the ESLint rule [No Magic Numbers](https://typescript-eslint.io/rules/no-magic-numbers/) will result in high value for your application with almost little effort. This rule is not included in the default config, so you will need to turn it on yourself.

Small, simple changes to your application (like using Value Defaults) contribute to a strong code base.
