## PointFree - Using the New Binding Functionality

# This is a Stub

When using SwiftUI controls, you can eliminate a lot of boilerplate by using the `@BindableState` property wrapper.

Each annotated field is directly bindable to SwiftUI controls, like pickers, toggles, and text fields.

- define a State variable using the @BindableState property wrapper:

```swift
 struct SettingsState: Equatable {
  @BindableState var displayName: String = “”
}
```
conform the action type to BindableAction by collapsing all of the individual, field-mutating actions into a single case that holds a BindingAction generic over the reducer's State

- add a notation to your Action enum:

```
 public enum GoldenAction: Equatable, BindableAction {
 ```

 - add a `binding` case (action) to your Action: 

```swift
  case binding(BindingAction<SettingsState>)
```

even if you don’t need to take any action after the state changes, you still need to add a call to the `binding` function of Reducer at the closing delimiter of the reducer:

```swift
}
.binding()
```

if you need to take some action, then you want to match the case in your reducer:

```swift
 case .binding(\.displayName):
    state.displayName = String(state.displayName.prefix(16))
    return .none
```

It’s all pretty straightforward and will become second nature with use.

[Next](06-TheComposableArchitecture.md)

.pct 18