# Two npm Packages for Every Frontend Project

If you are using a javascript or typescript frontend framework, use [ESLint](https://eslint.org/) with [Prettier](https://prettier.io/) and never look back.

ESLint is a linter that contains best practice coding standards, such as enforcing that function return types and variables are declared properly.

Prettier is a pure styling tool that automates spacing, semi-colons, and white space.

When put together and enforced via the compiler, your application becomes consistent throughout and provides a constant feedback loop to your developers on what is correct and what is not.

If you are using a recent version of React, Angular, or Vue application that was scaffolded via their respective CLI's, then you probably have one (or both) installed already.

These projects come with predefined rule sets and plugins that contain a majority of the rules needed. You should have to define very few if any custom rules.

## ESLint

If you are using Angular, the [angular-eslint](https://github.com/angular-eslint/angular-eslint) package can install everything you need and even remove tslint for you. If you are using React or Vue without ESLint, sadly there is no alternative to add it easily. My recommendation is to create another React or Vue project on the side and then copy the npm packages and configuration files into your project.

## Prettier

I highly recommend using the [eslint-config-prettier](https://www.npmjs.com/package/eslint-config-prettier) and [eslint-plugin-prettier](https://www.npmjs.com/package/eslint-plugin-prettier) plugins to have a more seamless experience integrating the two.

## Conclusion

A frontend project either with or without the one-two punch of ESLint and Prettier is night and day. Combining the two will enforce a consistent coding style that simplifies your job as manager and architect. ESLint and Prettier, in conjunction, keep a sterile development environment so that issues don't fester.
