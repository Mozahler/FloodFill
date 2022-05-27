## Integrating TCA

We’ve covered most of the ground work in preparation for this final part of the project.  Now that we have a good understanding of how fundamental bindings are to getting things done we can apply what we know about TCA and functional programming to add functionality to our (already working) project.

Before we begin I want to mention (again) that SwiftUI provides a declarative way to provide two-way communication between controls (buttons, links, pickers, counters) and the source of truth, while TCA enforces more of a one-way flow, where the work is funneled through a reducer which steps through the actions required by any user initiated event (such as a tap).

The nice thing about this paradigm is that it becomes second-nature over time, and when problems occur you have a good understanding of where you need to look to solve your issue.

So far we haven’t even discussed the benefits to testing.

Here’s a quick review of how we like to structure our code using TCA:

### State

The State object keeps track of properties that drive our UI.  For our project we’re only using one property. `animationDuration` is used to drive the speed of the animation - from lightning fast to slow as molasses.

### Action

This is an enum with a case for each action that is supported. The reducer will iterate through the cases to perform any required actions, using any associated values that are passed in. With the advent of `@BindableState` and `@BindableAction` we also channel updates through the `.binding` action. Declaring that the `Action` struct conforms to the `BindableAction` protocol will make the compiler remind you that you need this case as well.

### Environment

The environment struct is where side effects are performed. We have no network calls, and nothing we do would take so long it would hang the system, so we are going to be ignoring this one.

### Reducer

The reducer is the mechanism that drives the change of state. You are not allowed to assign values directly to the `State` object. You can send any of the actions (cases) covered by the `Action` object, and the reducer is where this call is resolved. With @BindableState you also have the option to perform processing when one of these State variables is updated - via the use of `.binding()`

### Store

The store acts as the glue that holds all the pieces together. When the store is created it requires a `State`, `Environment` and `Reducer` object. From this `Store` object we will typically create a ViewStore object which we will use to send actions via the reducer. The `State` object is an `inout` variable (it can be updated) while the `Action` is an enum.

Believe it or not, we are going to cover all the bases with just one `State` variable.

## Defining our `State` Object

This couldn’t be easier:

```swift
public struct GameState: Equatable {
   @BindableState var animationDuration = AnimationSpeed.allegro.rawValue // index of picker
}
```

The Picker returns an Int which indicates which segment was tapped. By `returns` I mean that it updates a bound variable.

```swift
Picker("AnimationSpeed", selection: viewStore.binding(\.$animationDuration)) {
   ForEach(Array(AnimationSpeed.allCases.enumerated()), id: \.0) { index, segment in
      Text(segment.description).tag(index)
   }
}
```

Normally when using a picker the selection parameter is something like:

```swift
$animationDuration
```

but there’s a little but of boilerplate required to tap into the TCA way of doing things:

```swift
viewStore.binding(\.$animationDuration))
```

Take a couple of minutes to completely understand the difference in syntax, especially consider that you typically are replacing a `@State` (private, local) variable with a binding to a variable in the `GameState` object. One advantage to this approach is that the value is available outside this one View.

We’re going to follow the trail so you can see why this extra bit of boilerplate is entirely worth it.

## Our Very Simple Action Enum

I could dedicate an entire unit to discussing how the guys at PointFree have helped the transition of the enum into a First Class DataType with CasePaths, and perhaps I will at a later date. But for those who don’t use enums a lot, this is a gentle introduction.

```swift
public enum GameAction: Equatable, BindableAction {
   case binding(BindingAction<GameState>)
   case incrementSeed
   case resetSeed
}
```

We support the binding for our Picker using `@BindableState` and `BindableAction`, plus a couple of actions using the traditional TCA approach. Typically, the action holds quite a few actions/cases, so this is definitely a simplified presentation. I want you to get your feet wet, not drown in a deluge of new coding practices.

Be on the lookout (or click on issues and open an Issue to request) further presentations. I have a handful in the works.

## The Environment

As mentioned above, we won’t be dealing with side effects in this app, and so our Environment object will remain empty:

```swift
public struct GameEnvironment { public init() {} }
```

## The Reducer

The reducer normally performs a switch over the action passed in. That means each case in the `GameAction` enum must be handled. Once each case is covered, we will be returning `.none`, which is shorthand for Effect.none. The reducer always returns an Effect, but in our case we have handled everything each time, and do not pass anything back.

For the first two cases, we simply call our data model which will handle updating the seed and creating a new grid with a different set of color values.  These cases are here to show you how processing works when not using `@BindableState` variables.  The reducer is the heart of the process, as it is the mechanism that moves the State forward. Typically it would be processing a much larger `State` enum than the one you see here.

As mentioned above, the `@BindableState` variable defined in the `GameState` object is updated by the Picker. The `GameAction` conforms to `BindableAction`, and so a call to `.binding` will trigger the third case - look for the `.send` statement above in the `Picker` code to refresh your memory of the sequence of events).

The `animationDuration` variable represents the index of the tapped segment in the picker. Here we instantiate an enum using this index as the rawValue. Enum initializers with raw values are failable, so we need to make sure we get a valid value before proceeding. Once we have an `AnimationSpeed` object we pass it to the data model for processing.

```swift
public let gameReducer: Reducer<GameState, GameAction, GameEnvironment> = .init { state, action, environment in
   print("action:", action)
   switch action {
   case .incrementSeed:
      GridModel.shared.incrementSeed()
   case .resetSeed:
      GridModel.shared.resetSeed()
   case .binding(\.$animationDuration):
      if let selection = AnimationSpeed(rawValue: state.animationDuration) {
         GridModel.shared.updateSpeed(selection)
      }
   case .binding(_):
      ()
   }
   return .none
}
.binding()
```

Notice the very last line of code.  

You’ll be very frustrated if you leave it out. This function (defined on Reducer) returns a reducer that applies ``BindingAction`` mutations to `State` before running this reducer's logic. In other words, when the case `.binding(\.$animationDuration)` is processed within the reducer’s switch statement, the State variable has already been updated after the tap (or whatever triggered the change in state. So you are free to work with the new value (in this case we pass it on to our data model)

## Links

[Mozahler - Full FloodFill](https://github.com/Mozahler/FloodFill)

.pct 78