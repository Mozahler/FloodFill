## Binding, Property Wrappers

# This is in progress - revised/enhanced file available soon

[Create a list of Property Wrappers and Protocols along with where they are defined. Use this to group the information presented]
### The `Combine` Framework

Bindings and Property Wrappers are not unique/specific to the `Combine` framework, but since some of the ones we will be using are defined there I think it makes sense to spend a few lines discussing what Combine brings to the table in our app


State, StateObject, ObservedObject, Binding, 
BindableState, BindingAction
Reducer.binding()

Related protocols:
ObservableObject (Protocol in `Combine`),  

Not discussing @Published, 
Publishers: Just, 

### Unexplored For Now
Skipping the entire world of resultBuilder, viewBuilder, etc.
but provide a brief discussion of their place as well as a few links for the curious.

### What is a Binding?

SwiftUI’s Binding property wrapper provides a way to establish a two-way binding between a given piece of state and any view that needs to modify that state. Usually you create a binding by referencing the state property using the \$ prefix.

[Finish making the case for SwiftUI layout updates and the use of Combine Publishers to provide a declarative rather than imperative approach to coding.  Explain how neither comes automatically, it’s in how the tools are used. You can use a hammer to drive in a screw, but it doesn’t mean you’ll be happy with the results.]

A binding doesn’t exist in a vacuum. SwiftUI provides several tools to help you manage the flow of data in your app. This approach is achieved by establishing a publisher and subscriber binding between the data and the views in the user interface

### State, StateObject, Binding, ObservableObject

@State - declares local value property. Apple recommends that you mark them private, because a @State property since the view manages its lifetime locally. 

@StateObject is designed to be the owner of the data. You initialize it right there and thus you know exactly where it comes from.
 
@ObservableObjects only update when @Published properties change. This allows you to write objects that can cache values and only change the @Published property when a final value is ready. Conversely any changes you make to the properties of a struct are immediately reflected, causing the view to be rendered anew. You can replicate the behavior in the struct by having a reference type property but it quickly becomes unwieldy as you tie the struct to your reference type
Also you can pass @ObservableObjects around in the environment with one @StateObject serving as the source of truth. You can’t do that with @State.

@ObservedObject - External reference property. Actually it is your data model that should be displayed.

>`@EnvironmentObject` A dynamic view property that uses a bindable object supplied by an ancestor view to invalidate the current view whenever the bindable object changes. It is similar to @ObservedObject, but it is used implicitly across the environment with other views, while @ObservedObject should passed explicitly. 

@Binding - defines that the property is not Source of Truth and is only the reference to real Source of Truth (@State, @ObservedObject, @EnvironmentObject). Passing one of the properties from 1-3 points to the view view must declare the property in child view as @Binding to create a reference and pass the property with $ sign like MessageDetails(message: $message) 

should I discuss the linked videos?

Binding is not what causes changes, it only passes them on

## Links

[Next](05-APropertyWrapperFromPointFree-BindableState)

[DataFlow - Morgun Ivan](https://en.proft.me/2021/05/22/data-flow-swiftui-state-binding-observedobject/)

[WWDC2019 - DataFlow and Swift](https://developer.apple.com/videos/play/wwdc2019/226/)

[WWDC2020 - Data Essentials in SwiftUI](https://developer.apple.com/wwdc20/10040)

[Sundell - Bindable SwiftUI List Elements](https://www.swiftbysundell.com/articles/bindable-swiftui-list-elements/)

.pct 22