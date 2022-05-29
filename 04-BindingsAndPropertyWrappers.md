## Binding and Property Wrappers

I provide here a brief summary of Binding and its relationship to Property Wrappers. It may seem strange to you that I spend so much time on these two topics, and then rarely mention them in the other chapters. But it's important to understand these concepts - they are fundamental to how SwiftUI works. A more complete discussion of property wrappers requires a discussion of the `Combine` framework. I will leave that for another day.

>When using TCA, we are (mostly) going to forego the usual use of `@State` variables.  Instead of having the View manage the variable, we are going to move it into our `GameState` object. By doing this we make it much easier to inject values into the app, making it more easily testable. Plus the variable can be shared with other views that use the same Store.

## What is a `Binding`

A binding in SwiftUI is a connection between a value and a view that displays and changes it (typically by using a `control` such as a Picker or Slider). You can create your own bindings with the `@Binding` property wrapper, and pass bindings into views that require them. Or you can use a property wrapper, like `@State`, which exposes a binding to its wrapped value. In the case of a `control`, the connection is two-way, so the UI element can change the value, and the value can change the UI element. Bindings make it easier to write declarative code, since the enable your code to respond to changes in the bound variables.

You access the binding using the `$` prefix. This is called the `projected value`. Use the projected value to pass a binding value down a view hierarchy.  When you pass a variable in a function call using the `$` prefix you are sending the binding, and can update the variable in the called routine. The caller will see these changes and can respond to them.

You access the wrapped value using the `_` (underscore) prefix - typically in the initializer of the child view when setting up a binding (example below)

Bindings and Property Wrappers is a big topic, and this dicussion is not exactly a deep dive. 
I will provide many links below for you to explore this topic on your own.
Please open an issue if you'd like to see a more in-depth look at these two topics.


```swift
struct SomeParentView: View {
   @State var shouldDisplayText: Bool = false
   
   ...
   
   SomeOtherView(shouldPrint: $shouldDisplayText)
   ...
   if shouldDisplayText {
      Text("The child view enabled this text")
   }
}
   ...
}
   
struct SomeOtherView: View {
   @Binding var shouldDisplayText: Binding<Bool>
   
   init((shouldDisplayText: Binding<Bool>) {
      _shouldDisplayText = shouldPrint
   }
   ...
   shouldDisplayText = true
   ...
}
```

Working with the above code, here are a few observations:
- `shouldDisplayText` is defined by the parent view.
- because it is a `@State` variable, it is owned by this (parent) view
- this bears repeating: `@State` has special properties that allow its wrapped value to remain intact when the view is recreated (typically due to a mutation or update to some property)
- `$shouldDisplayText` is a binding to the wrapped value (in this code it is false until set to true in the child view)
- The wrapped value can be accessed using a leading underscore: `_shouldDisplayText`
- Since a binding is passed to the child view's initializer, `SomeOtherView` can change the value from true to false
- The parent view can respond to changes in the `shouldDisplayText` value made by the child view.

So far I've glossed over the definition of what makes a property wrapper, but now that you've seen how it's declared and used, the following information has a better chance of `sticking`.

## Property Wrappers

When I first looked up property wrappers, this is what I found:

>[Property wrappers] Allow you to extract common logic in a distinct wrapper object

I hate circular definitions.  This doesn't explain anything to me.

>A property wrapper can be seen as an extra layer that defines how a property is stored or computed on reading. It’s especially useful for replacing repetitive code found in getters and setters of properties.

At least this tells me why I might want to consider using one.

A property wrapper is a generic structure that encapsulates read and write access to the property and adds additional behavior to it. We use it if we need to constrain the available property values, add extra logic to the read/write access (like using databases or user defaults), or add some additional methods.

This is maybe a bit more than we need right now. I will defer discussion of the role of generics for a different day.

>Although it would be convenient to think that everything that starts with an `@` is a property, this just isn’t the case. Keywords starting with an @ are instructions to the compiler, and signaling a property wrapper is just one of its uses.

I'm going to close out this discussion with a brief summary of some very common SwiftUI property wrappers.

## `@State`

State is the simplest source of truth your app can have. It is designed to contain simple value types, such as Ints, Strings, and Bools. It is not designed for more complex, reference types, such as any classes or structs you define yourself and use within your app.

>Whenever a `@State` variable is updated, the view itself is refreshed/recreated to reflect the new value.

#### @State
`@State` - this wrapper declares local value property. Apple recommends that you mark `@State` variables  private, because the view manages its lifetime locally. 

>[David Pasztor](https://stackoverflow.com/questions/61361788/state-vs-observableobject-which-and-when) If you mark any variables as @State in a SwiftUI View and bind them to a property inside the body of that View, the body will be recalculated whenever the @State variable changes and hence your whole View will be redrawn. Also, @State variables should serve as the single source of truth for a View. For these reasons, @State variables should only be accessed and updated from within the body of a View and hence should be declared private.

### `@StateObject`
 a `@StateObject` is also designed to be the owner of the data. You initialize it right there and thus you know exactly where it comes from.
 
 ## `@StateObject`

>A property wrapper type that instantiates an observable object (which is a type of object with a publisher that emits before the object has changed)

SwiftUI’s @StateObject property wrapper is a specialized form of @ObservedObject, having all the same functionality with one important addition: it should be used to create observed objects, rather than just store one that was passed in externally.

When you add a property to a view using @StateObject, SwiftUI considers that view to be the owner of the observable object. All other views where you pass that object should use @ObservedObject.


By using @StateObject, we are letting our view know that whenever any of the @Published properties within the Observable Object change, we want the view to re-render - we are now listening for changes to any of its marked @Published properties.

>A thorough explanation of `@Published` would neccesitate covering the `Combine` framework. This needs to be left for another day - please open an issue if you'd rather see it appear sooner than later.

SwiftUI’s @StateObject property wrapper is designed to fill a very specific gap in state management: when you need to create a reference type inside one of your views and make sure it stays alive for use in that view and others you share it with.
 
You should use @StateObject only once per object, which should be in whichever view is responsible for creating the object. All other views that share your object should use @ObservedObject.

>[David Pasztor] If you mark any variables as @State in a SwiftUI View and bind them to a property inside the body of that View, the body will be recalculated whenever the @State variable changes and hence your whole View will be redrawn. Also, @State variables should serve as the single source of truth for a View. For these reasons, @State variables should only be accessed and updated from within the body of a View and hence should be declared private.

### When to use either of these two

You should use @State when you are binding some user input (such as the value of a TextField or the chosen value from a Picker). @State should be used for value types (structs and enums).

On the other hand, @ObservedObject should be used for reference types (classes), since they trigger refreshing a view whenever any @Published property of the ObservableObject changes.

You should use @ObservedObject when you have some data coming in from outside your View, such as in an MVVM architecture with SwiftUI, your ViewModel should be stored as an @ObservedObject on your View.
 
## The `ObservableObject` Protocol

This protocol is defined in `Combine`, and it defines a type of object with a publisher that emits before the object has changed. This effectively means that when @Published properties in an `ObservableObject` change, your view is notified of the change so it can respond to the change.

`ObservableObjects` change the @Published property when a final value is ready. Conversely any changes you make to the properties of a `View` struct are immediately reflected, causing the view to be rendered anew immediately. This is part of the "push-pull" dichotomy of struct vs. class (value vs. reference types)


## `@ObservedObject`

A property wrapper type that subscribes to an observable object and invalidates a view whenever the `ObservableObject` changes. In contrast to the earlier wrappers, `@ObservedObject` is used to keep track of an object that has already been created.

I have left out many useful property wrappers in this discussion, but I have covered enough of the groundwork for you to start to make your own connections when you see a property wrapper defined or used. And you will see them frequently.


## Links
 
[Next](05-APropertyWrapperFromPointFree-BindableState.md)  
[HackingWithSwift - Property Wrappers](https://www.hackingwithswift.com/quick-start/swiftui/all-swiftui-property-wrappers-explained-and-compared)  
[DougGregor on GitHub](https://github.com/DougGregor/swift-evolution/blob/property-wrappers/proposals/0258-property-wrappers.md)
[Mutating Structs](https://stackoverflow.com/questions/24035648/swift-and-mutating-struct)
[Sam Wright](https://purple.telstra.com/blog/swiftui---state-vs--stateobject-vs--observedobject-vs--environme)  
[Hacking](https://www.hackingwithswift.com/quick-start/swiftui/what-is-the-stateobject-property-wrapper)  
[Apple OnLine Swift Documentation - Attributes](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html)  
[DataFlow - Morgun Ivan](https://en.proft.me/2021/05/22/data-flow-swiftui-state-binding-observedobject/)  
[WWDC2019 - DataFlow and Swift](https://developer.apple.com/videos/play/wwdc2019/226/)  
[WWDC2020 - Data Essentials in SwiftUI](https://developer.apple.com/wwdc20/10040)  
[Sundell - Bindable SwiftUI List Elements](https://www.swiftbysundell.com/articles/bindable-swiftui-list-elements/)  
[Binding](https://www.appypie.com/binding-swiftui-how-to)

.pct 82
