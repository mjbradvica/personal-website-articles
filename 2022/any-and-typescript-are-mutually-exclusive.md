# Any and TypeScript are Mutually Exclusive

One of the best additions to the front-end development landscape was TypeScript. It tames the wild west programming environment of JavaScript by providing support for types. TypeScript also added the ability to have live intellisense and interfaces for our objects. Front-end development is a lot better and easier today with the addition of TypeScript.

TypeScript is a superset of JavaScript; at the end of the day everything will be transpiled back into JavaScript. This means that anything you can do in JavaScript, you still can do in TypeScript. It may be a flaw, but it's something that we as developers need to be aware of.

One of the biggest danger zones facing developers is the ability to still use the 'any' type, a disregard for type checking.

> There's no point in using TypeScript if you allow the type 'any' to exist.

## Choice One, Avoid the Other

Choosing TypeScript is just one step of many to keeping 'any' out of an application. The use of 'any' will metastasize like a cancer if you allow it to proliferate in an application. You must be proactive with your approach, including but not limited to:

* Enabling [strict mode](https://www.typescriptlang.org/tsconfig#strict) in your tsconfig
* Forcing the [no-explicit-any](https://typescript-eslint.io/rules/no-explicit-any/) to an error
* Avoiding any third-party library that has excessive use of 'any' in function calls or interfaces

If you still want to use the 'any' type, don't use TypeScript. The usage defeats the purpose of enabling type checking in the first place.

## Unknown and Never are Just as Bad

Unfortunately it's not just the 'any' type we need to avoid, 'never' and 'unknown' are just as bad. Neither are real types to begin with, they exist as a no-mans land between the type safe world, and the chaotic universe of 'any'. You should employ the same zeal against their usage as you do with 'any'.

## Even Stricter Typing

If you are already using ESLint, [which you should](https://michaelbradvica.com/two-npm-packages-for-every-frontend-project). Buy yourself even more insurance for your project by using the following built-in configurations:

* [Required Type Checking](https://typescript-eslint.io/docs/linting/configs#recommended-requiring-type-checking)
* [Strict Rule Set](https://typescript-eslint.io/docs/linting/configs#strict)
  
These two extra configurations will enable more typed rules to help avoid unnecessary and costly bugs.

Choosing TypeScript is just one step in moving to a better development environment. Adding TypeScript to your application is not enough. You must retain a fervent dedication to the idea of strict types in your application.
