# Avoid Bad Types

> This article is about application level code. Library code differs.

TypeScript is merely a different flavor of JavaScript. Anything you can do in TypeScript gets transpiled back to plain vanilla JS. You can still do damage to your application by allowing bad programming to seep slowly into your code base.

A quick fix is to double-check that "strict" mode is enabled in your ts config.

## tsconfig.json

```json
{
  "compilerOptions": {
    // Lots of extra junk
    "strict": true,
  },
}
```

Strict mode will turn on [eight separate rules](https://www.typescriptlang.org/tsconfig#strict) that put you on the right path for keeping TypeScript free from bad influences; however, this is not enough. You also need to keep other bad types and practices out of your code base. The best procedure for this is to lean on your linter.

The more you treat TypeScript like any other static-typed language such as C# or Java the better your code base becomes.

## Extending Ban Types

Banning certain types is rooted in removing uncertainty and undefined behavior from your application. More certainty in your application translates to a higher level of confidence that-it will meet expectations.

Our weapon of choice is to extend the [ban-types](https://typescript-eslint.io/rules/ban-types/) rule in ESLint.

We are adding "never", "unknown", "object", and and the "empty object" to our ban list. This will remove problem causing types from our application, forcing us to stick with static type instead.

```json
"rules": {
    // other rules
    "@typescript-eslint/ban-types": [
      2,
      {
        "types": {
          "never": "Use a proper type.",
          "object": "Use a proper type.",
          "unknown": "Use a proper type.",
          "{}": "Use a proper type."
        },
        "extendDefaults": true
      }
    ]
  }
```

## Others to Avoid

### Enums over Inferred Literal Types

In TypeScript, you can have a function that has the following kind of return type:

```typescript
export function directions(goLeft: boolean): "left" | "right" {
  if (goLeft) {
    return "left";
  }
  return "right";
}
```

This is a leaky abstraction. We should use an enum instead.

```typescript
enum Directions {
  Left = "Left",
  Right = "Right",
}

export function directions(goLeft: boolean): Directions {
  if (goLeft) {
    return Directions.Left;
  }
  return Directions.Right;
}
```

Our refactored code does not provide a leaky abstraction-and, therefore, is much cleaner.

### Interfaces over types

If you are already using ESLint's strict mode, and you should be, then the [consistent-type-definitions](https://typescript-eslint.io/rules/consistent-type-definitions/) rule is already enabled.

While differences between interfaces and types in TypeScript are minute, you should stick with interfaces. Interfaces are more straightforward and easier for other developers to utilize.

Keeping abstract and non-static types out of your front-end code will give you more predictability, easier-to-test code, and a more maintainable code base.
