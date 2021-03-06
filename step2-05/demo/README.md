# Step 2.5 - Redux: Reducers (Demo)

[Lessons](../) | [Exercise](./exercise/) | [Demo](./demo/)

## Flux

Ideally React gives us a mental model of:

```
f(data) => view
```

We can pass shared state data down to components using props and update it using callbacks, but as an application grows larger, passing around callbacks becomes unwieldy and the state data flow becomes hard to understand. To keep the application maintainable, we need a well-defined pattern for sharing and updating application state.

Facebook invented the [Flux](https://facebook.github.io/flux/) architectural pattern to solve this shared state issue. [Redux](https://redux.js.org/) is an implementation of Flux.

Redux is used inside many large, complex applications because of its clarity and predictability. It is easy to debug and easily extensible via its middleware architecture. In this lesson, we'll explore the heart of how Redux manages state.

Redux expects the data (store) to be a singleton state tree. It listens for messages to manipulate the state and passes updates down to views.

![Flux Diagram](../assets/flux.png)

### View

A view is a React component that consumes the store as its data. There is a special way Redux maps data from the state tree into the different React components. The components will know to re-render when these bits of state are changed.

### Action

[Actions](https://redux.js.org/basics/actions) are messages that represent some event, such as a user's action or a network request. With the aid of *reducers*, they affect the overall state.

### Store

The [store](https://redux.js.org/basics/store) contains a singleton state tree. The state tree is immutable and needs to be re-created at every action. This helps connected views easily to know when to update by allowing them to do a simple reference comparison rather than a deep comparison. You can think of each state as a snapshot of the app at that point in time.

### Dispatcher

There is a single [dispatcher](https://redux.js.org/basics/data-flow), provided by the store. It simply informs the store of all the actions that need to be performed. Additional middleware (explained later) can be applied to the store, and the dispatcher's job is to dispatch the message through all the middleware layers.

### Reducers

Redux uses [reducers](https://redux.js.org/basics/reducers) to manage state changes. This name comes from the "reducer" function passed to `Array.reduce()`.

A Redux reducer is a simple **pure function** (no side effects). Its only job is to transform the state from one immutable snapshot to another. It takes state + an action message as input, makes a modified copy of the state based on the action message type and payload, and returns the new state. (Reducers [should not modify](https://redux.js.org/introduction/three-principles#state-is-read-only) the previous state.)

**Mental Model**: Think of a reducer as part of the store. It should have no side effects and only define how data changes from one state to the next given action messages.

### Advanced: Middleware

From the [documentation site](https://redux.js.org/advanced/middleware):

> Redux middleware provides a third-party extension point between dispatching an action, and the moment it reaches the reducer.

We won't be covering middleware much in these lessons since it's a more advanced topic.

## Getting started with Redux

We begin the journey into Redux by looking at the store. The store consists of several parts:

1. **State/data** - We represent this both with an initial state and with a TypeScript interface.
2. **Reducers** - Responsible for reacting to action messages to change from one state to the next.
3. **Dispatcher** - There should be only one dispatcher, which is exported by the store. We'll look at this in Step 6!

### Create store

The [`createStore()`](https://redux.js.org/api/createstore) function is provided by Redux and can take in several arguments. The simplest form just takes in reducers.

```ts
const store = createStore(reducer);
```

`createStore()` can also take in an initial state. There are two common sources for the initial state:

1. Load initial data from a server
2. Load data that is generated by a server-side rendering environment

```ts
const store = createStore(reducer, {
  /* the initial state */
});
```

`createStore()` can take a third argument that injects middleware, but we won't use this until later.

### Reducers

Remember that the [reducers are **pure**](https://redux.js.org/introduction/three-principles#changes-are-made-with-pure-functions). Pure functions have no side effects. They always return the same output given the same input (idempotent). They are easily testable.

Reducers look at the action's message to decide what to do to the state. A convention established in the Flux community is that the action message (payload) should include a `type` key. Another convention is using switch statements against the `type` key to trigger further reducer functions.

```ts
function reducer(state: Store['todos'], payload: any): Store['todos'] {
  switch (payload.type) {
    case 'addTodo':
      return addTodo(state, payload.id, payload.label);
  }

  return state;
}
```

In the demo and exercises for this step, I separated the pure and reducer functions into different files to make it cleaner. The tests inside `pureFunctions.spec.ts` should describe the behavior of the individual functions. They are easy to follow and easy to write.
