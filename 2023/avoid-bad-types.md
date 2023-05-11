# Avoid Bad Types

> I am talking about application level code here. Library code differs.

TypeScript is just a different flavor of JavaScript. Anything you can do in TypeScript just gets transpiled back to plain vanilla JS. You can still do damage to your application by allowing for bad programming to slowly seep into your code base.

A quick two second fix is to double-check that "strict" mode is enabled in your ts config.

## tsconfig.json

```json
{
  "compilerOptions": {
    // Lots of extra junk
    "strict": true,
  },
}
```

Strict mode will turn on [eight separate rules](https://www.typescriptlang.org/tsconfig#strict) that put you on the right path for keeping TypeScript clean of bad influences. However, this is not enough. You need to explicitly keep other bad types and practices out of your code base. The best procedure for this is to lean on your linter.

The more you treat TypeScript like any other static-typed language such as C# or Java the better your code base becomes.

## Extending Ban Types

Banning certain types is rooted in removing uncertainty and undefined behavior from your application. The more certainty your application provides translates to a higher level of confidence it will perform to your expectations.

Our weapon of choice is to extend the [ban-types](https://typescript-eslint.io/rules/ban-types/) rule in ESLint.

We are adding never, unknown, object, and and the empty object to our ban list. This will remove problem causing types from our application--forcing us to stick with static type instead.

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

## Enums over Inferred Literal Types

In TypeScript you can have a function that has this kind of return type:

```typescript
export function directions(goLeft: boolean): "left" | "right" {
  if (goLeft) {
    return "left";
  }
  return "right";
}
```

This is a leaky abstraction. We should use a enum instead.

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

Our refactored code does not provide a leaky abstraction, and is much cleaner.

## Interfaces over types

If you are already using ESLint's strict mode. [And you should.]() The [consistent-type-definitions](https://typescript-eslint.io/rules/consistent-type-definitions/) rule is already enabled.

While the differences between interfaces and types in TypeScript are minute, you should stick with interfaces. Interfaces are more straight forward and easier for other developers to work with.

Keeping abstract and non-static types out of your front-end code will give you more predictability, easy-to-test code, and a more maintainable code base.
