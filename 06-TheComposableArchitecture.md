## The Composable Architecture

>There’s a lot of “heavy lifting” in this chapter, and it’s not going to all come together on your first read. In fact, my recommendation is to skip over this for now, and come back if you get stumped while working out the details of hooking up the picker and buttons (later in this project.)

## Overview

>[From PointFree](https://www.pointfree.co) The Composable Architecture (TCA, for short) is a library for building applications in a consistent and understandable way, with composition, testing, and ergonomics in mind. It can be used in SwiftUI, UIKit, and more, and on any Apple platform (iOS, macOS, tvOS, and watchOS).

>[TCA] provides a few core tools that can be used to build applications of varying purpose and complexity. It provides compelling stories that you can follow to solve many problems you encounter day-to-day when building applications, such as:

* **State management**

    How to manage the state of your application using simple value types, and share state across many screens so that mutations in one screen can be immediately observed in another screen.

* **Composition**

    How to break down large features into smaller components that can be extracted to their own, isolated modules and be easily glued back together to form the feature.

* **Side effects**

    How to let certain parts of the application talk to the outside world in the most testable and understandable way possible.

* **Testing**

    How to not only test a feature built in the architecture, but also write integration tests for features that have been composed of many parts, and write end-to-end tests to understand how side effects influence your application. This allows you to make strong guarantees that your business logic is running in the way you expect.

* **Ergonomics**

    How to accomplish all of the above in a simple API with as few concepts and moving parts as possible.

## History, Vocabulary and Concepts for TCA

The Composable Architecture was built on a foundation of ideas started by other libraries, in particular [Elm](https://elm-lang.org) and [Redux](https://redux.js.org/). (See links below)

>If you're not familiar with functional programming and generics all of this can feel like walking into the deep end of the pool. I'm going to try to break it down a little while avoiding delving to deeply into the mathematics, generics, enums and their associated values -- all key concepts that contribute to making everything work. Rome wasn't built in a day, and you're not going to learn the TCA architecture from one page of exposition.

>In functional programming you will often send functions as parameters. Wrap your head around this concept now.
>Generics are required because many of the types used are defined as protocols - they often don't have concrete/honest types unless we create them ourselves.

When learning the fundamentals of TCA, I would get stuck when a word came up and it was being used in a way I wasn't familiar with. I think it might help to go over some of this, breaking it into chunks, just so that it all makes a little more sense as a whole. 

### Let's Start with a **Trinity:**: State/Action/Environment

The organizing principles of TCA frequently lead to state, action and environment being passed around together. 
For example, when accessing the reducer -- a function that needs access to all three to do its work

* **State**: A type that describes the data your feature needs to perform its logic and render its UI. [Your Domain Model]

TCA enables you to manage the state of the application using simple value types, and share state across many screens so that mutations in one screen can be immediately observed in another screen. Maybe you're already using view models, what I'm referring to is not a lot different. Let the view be a view, do the math your reducer - it can farm tasks out to `Environment` if they’re cpu intensive or involve network calls. The reducer will update the state, and your View can respond to these changes.

* **Action**: A type that represents **all of the actions that can happen in your feature**, such as user actions, notifications, event sources and more.

* **Environment**: A type that holds any dependencies the feature needs, such as API clients, analytics clients, etc. Typically any long lasting computation that needs to be executed on a separate thread will be handled here.

These three are enhanced with two powerful additions

* **Reducer**: A **Struct** that describes evolving the current state of the app to the next state given an action. It is initialized from a simple reducer function signature. The reducer is also responsible for returning any effects that should be run, such as API requests, which can be done by returning an `Effect` value.  

>In my simpified presentation here, I do not return any Effects, other than Effect.none.   
  🔔🔔🔔 You should notice that we are using functional programming principles here. This is a very important piece of the puzzle.
  
* **Store**: The runtime that actually drives your feature. You send all user actions to the store so that the store can run the reducer and effects, and you can observe state changes in the store so that you can update UI.

Together they create what I call **The Mighty Five** [(The Five)](https://en.wikipedia.org/wiki/The_Five_(composers))
This moniker helps me remember them. Find one that works for you.  
- **State**  
- **Action**  
- **Environment**  
- **Reducer**  
- **Store**  

The `reducer` and `store` help provide **a consistent manner to apply state mutations**, as well as a concise way of expressing side effects. The better you understand the architecture, the more you will appreciate the importance of this to keeping things clean.

The reducer contains the logic of how the current state should change to the next state and what effects should be executed by the store, if any.

>It is good practice to split out a large reducer into smaller, composable reducers that work on local state.

The store contains the state, reducer, and environment for a SwiftUI view. 
It receives actions that are sent by the view store in order to run the reducers and any effects.

>It's good practice to initialize the entire global store at the root-level, but slice more local state into a child view using the scope method
The goal is to keep child views from knowing too much about what's happening outside of itself

### An Underlying Philosophy (or Organizing Principle)

**We want to be able to develop complex programs that are cleanly built up from manageable components.**

In Redux, **A reducer is a pure function** that takes an action and the previous state of the application and returns the new state. Similarly, in TCA, the reducer struct is driven by a switch over the cases of the Action enum, and uses its access to the state, actions, and an environment to change the state, and returns any effects to be executed by the store at a later time as necessary.

In essence, the reducer concept contains the logic of how the current state should change to the next state and what effects should be executed by the store, if any.

>It is good practice to split out a large reducer into smaller, composable reducers that work on local state. That’s a large topic unto itself, and please open an issue if you’d like to know more about how to further modularize the code by maintaining multiple sets of the **Mighty Five**

**Store**

The store contains the state, reducer, and environment for a SwiftUI view. That’s pretty much the whole package. It receives actions that are sent by the view store in order to run the reducers and any effects. You can create a view on the store which will allow you to observe and respond to changes in the state.

It's good practice to initialize the entire global store at the root-level, yet maintain local versions of state/action/reducer that can be specialized. The goal is to keep child views from knowing too much about what's happening outside of itself, and reducing unnecessary refreshes of Views.

**Store vs. ViewStore**

It can be a bit frustrating when you're trying to get all the terms down and some of them sound just each other. Store may be a new concept for you, and now I’m talking about a ViewStore. I briefly mentioned the marriage of store and a way to observe its changes as a ViewStore. This “view” or “window” into your state allows you to send messages via the reducer, and it is a key component of the system. Without it you won’t see the changes to state needed to update your view. And using the ViewStore to send messages that are processed by the reducer is the only way to update your state. Remember: **You do not have direct access to a mutable copy of the state.**

### Implementing the ViewStore

If any SwiftUI view needs access to the store in TCA, it must go through the ViewStore - an object that observes state and sends actions.

It can be used along with WithViewStore which is a custom SwiftUI view provided by TCA that takes in a store and returns a view. I prefer to set it up as an observable object in the View’s init(). You will see both methods used interchangeably.

>[From PointFree] Going through another object to get access to state and actions instead of just referencing the store inside of a view may seem unusual, but this was done to maximize performance. When state changes inside of the store, the store will publish its changes to all views who are subscribed to it and SwiftUI will re-invoke the body inside of that view.

Even if the parent is just passing a property down to a child view, the parent will re-render if it's subscribed to that change. Unnecessary re-renders are bad for performance, and the view store allows us to avoid that by focusing on only the state we need to.

### Triggering State Changes with Actions

As previously mentioned, state in TCA is read-only by design. This means that we can't write directly to state, but we can express our intent to change state by dispatching an action. The reducer that handles that action then responds by transforming state.

In a regular SwiftUI app, we'd update the state of the query with a two-way binding via the @Binding or @State or @StateObject property wrapper, but with TCA we want to prevent direct writes to state. For this scenario the `ViewStore` has a `.send` function which you use to send actions defined in the `Action` enum. These actions are processed by the reducer.

>**Instead of a direct state write, we only read state and declare an action to be sent through TCA when a state change should happen.**

## Help

If you want to discuss the Composable Architecture or have a question about how to use it to solve a particular problem, ask around on [its Swift forum](https://forums.swift.org/c/related-projects/swift-composable-architecture).

The latest documentation for the Composable Architecture APIs is available [here](https://pointfreeco.github.io/swift-composable-architecture/).

## See Also

[Elm Wiki](https://en.wikipedia.org/wiki/Elm_(programming_language)

>**Elm** is a domain-specific programming language for declaratively creating web browser-based graphical user interfaces. Elm is purely functional, and is developed with emphasis on usability, performance, and robustness. It advertises "no runtime exceptions in practice", made possible by the Elm compiler's static type checking. All values in Elm are immutable, meaning that a value cannot be modified after it is created. Elm uses persistent data structures to implement its Array, Dict, and Set libraries (this might remind you of how we use structs in Swift and SwiftUI) Elm has a module system that allows users to break their code into smaller parts called modules. One of the many benefits of the TCA is that it, too, lends itself to modularization - which allows you to concentrate on one area of your project in isolation from the rest.

[Redux Wiki](https://en.wikipedia.org/wiki/Redux_(JavaScript_library)

>**Redux** is an open-source JavaScript library for managing application state. It is most commonly used with libraries such as React or Angular for building user interfaces. Redux is a predictable state container for JavaScript apps. It helps you write applications that behave consistently, run in different environments (client, server, and native), and are easy to test. Redux is used mostly for application state management. To summarize it, Redux maintains the state of an entire application in a single immutable state tree (object), which can't be changed directly. When something changes, a new object is created (using actions and reducers) -- Again: this preference for immutability should remind you of structs in the Swift world.

Check out the collection of videos from [Point-Free](https://www.pointfree.co) that dive deep into the development of the library.

* [Point-Free Videos](https://www.pointfree.co/collections/composable-architecture)


## Other Links

[Next](07-IntegratingTCA.md)

- [GitHub Repo](https://github.com/pointfreeco/swift-composable-architecture)
- [Discussions](https://github.com/pointfreeco/swift-composable-architecture/discussions)

.pct 80
