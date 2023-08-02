# Differentiating between Local, Intermediate, and Global state

In modern front-end applications, we have three primary means of applying state-local, intermediate, and global. Each has a specific use-case.

It is important to know where and when to use each kind of state. It may be the difference between a good application, and a great application. State types also build off of each other-intermediate state uses local state under the hood, and global state uses intermediate.

## Local

Local state concerns the [useState](https://react.dev/reference/react/useState) hook React, [ref](https://vuejs.org/api/reactivity-core.html#ref) in Vue, or [signals](https://angular.io/guide/signals) in Angular. These are the basic building blocks in each framework. If you don't know what kind of state you require, there is a high chance you just want normal local state.

## Intermediate

Intermediate state builds on local state in that you are taking a local state variable an introducing a switch statement. It is referred to intermediate state because it solves situations where you need more than just local state, but global state would be overkill.

> Intermediate state = local state + switch statement.

The common pattern to use for intermediate state is a reducer. A reducer simply encapsulate's the local state variable while exposing the state and a dispatch function. If you are using React, the [useReducer](https://react.dev/reference/react/useReducer) hook is build into the framework. Vue and Angular uses do not fret, it is an [easy concept to copy.](https://michaelbradvica.com/mimicking-use-reducer-in-vue-and-angular)

You application can only read the current state and dispatch actions which are used by the switch statement. This keeps your state encapsulated and pure to avoid undefined behavior into the system.

Applying intermediate state requires you to ask the following questions:

- Am I drilling props though many components?
- Do I have lots of callbacks going to a single state variable?

If you answered yes to either of these questions, then you may want to consider using intermediate state. The dispatch function that is returned form a reducer hook is a form of [Command](https://en.wikipedia.org/wiki/Command_pattern). Where we are taking N number of parameters and condensing them down in a single object. This allows us to reduce the number of callbacks that need to be passed to children-thus reducing our props count.

Look at the following code:

```typescript
interface MyComponentProperties {
  isValid: boolean;
  name: string;
  updateValid: (isValid: boolean) => void;
  updateName: (name: string) => void;
  clearAll: () => void;
}
```

Having to pass these properties though three or more components would get old fast. Converting to intermediate state via a reducer can drastically cut our workload.

```typescript
interface CombinedState {
  isValid: boolean;
  name;
  string;
}

enum ActionType {
  UpdateValid = 0,
  UpdateName = 1,
  ClearAll = 2,
}

interface IAction {
  actionType: ActionType;
}

interface MyNewComponentProperties {
  state: CombinedState;
  dispatcher: (action: IAction) => void;
}
```

As you can see, you need to wire up a few new types to enable intermediate state. It is the main disadvantage we need to be aware of. Is the extra work worth not having a large props list?

> It is a good rule to not use intermediate state unless you have at least five or more properties you are passing to multiple children.

## Global
