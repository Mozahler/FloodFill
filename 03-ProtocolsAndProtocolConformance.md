## Protocols

# This is a Stub! - Final File coming soon

### The Compiler is Your Friend

Hating your compiler will not get you far. Sure: the compiler is an enormous beast and everything is it’s way or the highway. It’s frustrating when it says you’re doing something wrong - but what’s the alternative?

I started coding with C/C++ (after flirting with Pascal, Prolog and IBM 8086 Assembly Language). When working with C you got to overrule the compiler a lot. It’s not really a good habit to get into.

### The Benefits of a Strongly Typed Language

Swift is a strongly typed language.  There are many reasons for this, but I have to say that it brings many benefits.

### What Makes a Protocol

discuss them in general, discuss the ones used in the project

why are they important
swift as a protocol oriented language (vs. say object oriented)
explain as well as possible without involving Generics (save for another tutorial)

Identifiable
BindableState, BindableAction, 
Hashable and Equatable
// Codable
ViewModifier
View

### Where Do They Come From?

Whether you realized it or not, almost all the structs and classes you work with everyday are defined in part by their conformance to protocols.

### Is `View` a `struct`?

Would you be surprised to find out that the omnipresent `View` is actually a Protocol? Once you start looking into how it is defined you will find all sorts of things. Result Builders, View Builders, ViewModifiers, each of them adding functionality without getting in your way. Open an issue on my GitHub page if you want to learn more about these language helpers.

.pct 18

