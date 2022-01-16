# Two npm packages for every frontend project

If you are using a javascript or typescript frontend framework, use [ESLint](https://eslint.org/) with [Prettier](https://prettier.io/) and never look back.

ESLint is a linter that contains best practice coding standards such as making sure non-void functions have a return statement and that variables are declared properly.

Prettier is a pure style enforcement tool that enforces spacing, semi-colons, and white space.

When put together and enforced via the compiler, your application becomes consistent throughout and provides a constant feedback loop to your developers on what is correct and what is not.

If you are using a recent version of React, Angular, or Vue that was scaffolded via their respective CLI's, then you probably already have one or both installed already.

These projects already have predefined rule sets and plugins that already have a majority of the rules you need defined and ready to go. You should have to define very few if any custom rules.

## ESLint with Angular

If you are using Angular, the [angular-eslint](https://github.com/angular-eslint/angular-eslint) package can install everything you need, and even remove tslint for you.

## ESLing with React or Vue

If you are using React or Vue without ESLint, then sadly currently there isn't a option available to add it as easily as their is with Angular. My recommendation is to create another React or Vue project on the side and then copy the npm packages and configuration files over into your project.

## Prettier recommendations

I also highly recommend using the [eslint-config-prettier](https://www.npmjs.com/package/eslint-config-prettier) and [eslint-plugin-prettier](https://www.npmjs.com/package/eslint-plugin-prettier) plugins to have a more seamless experience integrating the two together.

## Conclusion

A frontend project with and without the one-two punch of ESLint and Prettier is a night and day comparison. Utilizing the two together will enforce a consistent coding style that will simplify your job as a manager and architect as you will have one less thing to worry about.
