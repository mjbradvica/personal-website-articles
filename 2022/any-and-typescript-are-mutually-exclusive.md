# Any and TypeScript are Mutually Exclusive

One of the best additions to the front-end development landscape was TypeScript. It tames the wild west of JavaScript by providing support for types. TypeScript adds live IntelliSense and interfaces for our objects. Front-end development is a lot better and easier today with the addition of TypeScript.

TypeScript is a superset of JavaScript; at the end of the day everything is transpiled back into JavaScript. This means that anything you can do in JavaScript, you still can do in TypeScript. It may be a flaw, but it's something we should keep in mind.

One of the biggest danger zones developers face is the option to use the 'any' type, disregarding type checking.

> There's no point using TypeScript if you allow the 'any' type to exist.

## Choose One, Avoid the Other

The use of 'any' will metastasize if you allow it to proliferate in an application. You must be proactive with your approach, including but not limited to:

- Enabling [strict mode](https://www.typescriptlang.org/tsconfig#strict) in your tsconfig
- Forcing the [no-explicit-any](https://typescript-eslint.io/rules/no-explicit-any/) to an error
- Avoiding third-party libraries that have excessive use of 'any' in function calls or interfaces

If you still want to use the 'any' type, don't use TypeScript. The usage defeats the purpose of enabling type checking in the first place.

## Unknown and Never are Just as Bad

Unfortunately it's not just the 'any' type that we should avoid. Both 'never' and 'unknown' are equally bad. These types exist as a no-man's-land between the type-safe world and the chaotic universe of the 'any' type. You should employ the same zeal against 'unknown' and 'never' as you do with the 'any' type.

## Even Stricter Typing

If you are not already using ESLint, [you should be](https://michaelbradvica.com/two-npm-packages-for-every-frontend-project). Buy yourself even more insurance for your project by using the following built-in configurations:

- [Required Type Checking](https://typescript-eslint.io/docs/linting/configs#recommended-requiring-type-checking)
- [Strict Rule Set](https://typescript-eslint.io/docs/linting/configs#strict)

These two extra configurations will enable more typed rules to help avoid unnecessary and costly bugs.

Choosing TypeScript is just one step in moving to a better development environment. Adding TypeScript to your application is not enough. You must retain a fervent dedication to the idea of strict types in your application.
