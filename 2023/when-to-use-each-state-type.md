# Differentiating between Local, Intermediate, and Global state

In modern front-end applications, we have three primary means of applying state-local, intermediate, and global. Each has a specific use-case.

It is important to know where and when to use each kind of state. It may be the difference between a good application, or a great one. State types also build off of each other-intermediate state uses local state under the hood, and global state uses intermediate.

## Local

Local state concerns the [useState](https://react.dev/reference/react/useState) hook React, [ref](https://vuejs.org/api/reactivity-core.html#ref) in Vue, or [signals](https://angular.io/guide/signals) in Angular. These are the basic building blocks in each framework. If you don't know what kind of state you require, there is a high chance you just want normal local state.

Use local state when:

- A single component needs to keep track of something locally
- A component needs to pass a minimum amount of state to a few children

## Intermediate

Intermediate state builds on local state by taking a local state variable and introducing a switch statement. It is referred to intermediate state because it solves situations where you need more than just local state, but global state would be overkill.

> Intermediate state = local state + switch statement.

The common pattern to use for intermediate state is a reducer. A reducer simply encapsulates the local state variable while exposing the state and a dispatch function. If you are using React, the [useReducer](https://react.dev/reference/react/useReducer) hook is built into the framework. Vue and Angular uses do not fret, it is an [easy concept to copy.](https://michaelbradvica.com/mimicking-use-reducer-in-vue-and-angular)

Applying intermediate state requires you to ask the following questions:

- Am I drilling props though many components?
- Do I have lots of callbacks going to a single state variable?

If you answered yes to either of these questions, then you may want to consider using intermediate state. The dispatch function that is returned from a reducer hook is a form of [Command](https://en.wikipedia.org/wiki/Command_pattern). Where we are taking N number of parameters and condensing them down in a single object. This allows us to reduce the number of callbacks that need to be passed to children-thus reducing our props count.

Looking at the following code:

```typescript
interface MyComponentProperties {
  isValid: boolean;
  name: string;
  updateValid: (isValid: boolean) => void;
  updateName: (name: string) => void;
  clearAll: () => void;
}
```

Having to pass these properties though three or more components would get old fast. Using a form of intermediate state allows you to condense your properties.

```typescript
interface CombinedState {
  isValid: boolean;
  name: string;
}

interface MyNewComponentProperties {
  state: CombinedState;
  dispatcher: (action: IAction) => void;
}
```

The biggest disadvantage to this process is the boilerplate that you need to add for a reducer. It requires you to define your actions, a reducer function, and any payload objects you are passing back to your reducer.

> It is a good rule to not use intermediate state unless you have at least five or more properties you are passing to multiple children.

Use intermediate state when local state is too little, but global state would be too much.

## Global

Global state in Angular, React, or Vue is done via large and complex libraries such as [Redux](https://redux.js.org/) or [NgRx](https://ngrx.io/). Vue's implementation [Pinia](https://pinia.vuejs.org/) is the easiest to understand because it's just a custom hook under the hood.

> Global state = reducer + global access

All three libraries are the reducer pattern that is injected in your application at a global level. When you add global state, you are allowing for global variables to potentially be accessed in components that should not have access to them.

> Global state use should be kept to an absolute minimum.

Use global state when:

- State may need to be used in many areas of the application that are in different component trees

The traditional means for using global state is to handle data that is associated with the authentication of your application. Another example may be the caching of data across browsers sessions.

## Conclusion

Choosing the correct state type in your application is a learning process. A vast majority of your state will be in normal local state variables. Only a small percentage should be in intermediate or global state. As always, the more you learn and experiment with state, the better you will be at applying it.
