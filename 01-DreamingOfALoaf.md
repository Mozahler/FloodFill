## Dreaming of a Loaf

I was wandering through the stacks at the library one day, and I picked up a slim volume called 
[The Little LISPer](https://mitpress.mit.edu/books/little-lisper-trade-edition).

Practically a pamphlet, it was a wonderful introductory text on the LISP programming language. The format was in the form of a question, followed by its answer. As I read it, it soon became obvious that you were being spoon-fed the information you needed to answer the next question - but only if you were paying attention. I loved it!

 Somewhere within its pages there was this great little parable about recursion. I read it very many years ago, and so this is definitely a paraphrase.

One day `Peter the Ponderer` was sitting down to his evening snack of a nice slice of fresh bread, slathered with some home-made preserves. Today it was a cranberry-orange marmalade in fact.  One of his favorites. The bread was fresh, so it wasn’t going to be a toast and jam day. (Not that there’s anything wrong with toast and jam!)

He cut himself a generous slice and began slathering on a thick layer of the marmalade. While he was spreading the spread, he got to thinking: “I wonder how many slices are in this loaf?” He pondered a bit, and as the events of the day caught up to him, he found himself drifting off to sleep.

In his dream it was about the time of sunset, and this is when he loved to partake of his favorite daily ritual: fresh bread and jam.  He went into the kitchen where he found his recently baked loaf. He had just picked it up at the baker’s the previous day, and it had but one slice gone.  Slicing off a piece of the loaf he wondered… “How many slices are in this loaf?” He thought about this for a bit and wasn’t quite sure how he was going to solve this problem. 

He was rapidly growing sleepy and didn’t really give it a whole lot of thought before being engulfed by slumber. As he started to dream, he found himself walking into the kitchen about to partake of his favorite daily ritual: fresh bread and jam. The bread wasn’t freshly baked, but that was actually a good thing: it meant he would be having a nice slice of toast that night coupled with any of his many choices of spreads. Maybe it would be honey tonight? Boysenberry?  Raspberry Rhubarb?  He chopped off a slice, went for the honey with pimento, and blissfully munched on his yummy toast while sipping on his favorite `SleepyTime` tea.  Before drifting off, he wondered to himself… “I wonder how many slices of bread make up this loaf.” And before you know it, he was sound asleep.

I’m sure you’re picking up on this pattern… as the loaf shrinks in size we are a dream within a dream within a dream. Finally Peter enters the kitchen and finds there is only one slice left of his beloved loaf of bread. He dutifully smears his topping of the day on his generous slice, and swallows the tasty snack down. As he looks at the crumbs on the plate, he suddenly notices that the loaf is all gone. And — poof — he wakes from this dream realizing that he just had the last slice of bread.

But he didn’t wake all the way up, he just woke up from the dream within the dream (within the dream, and so on), to the one he just dreamt. He thought to himself… “Oh, I just had the last slice in my dream and so this slice I just ate was my second to last slice.” and — poof — he woke up from this dream to realize, "oh there are two more slices to the loaf." 

And so forth, and so on, until he woke up from the very first dream and realized that there are 11 more slices of bread remaining on his freshly cut loaf.

That’s how you solve a puzzle recursively.

### The Iterative Solution

Peter was driving himself crazy.  He just had the first piece of the magnificent loaf he had purchased that day. What was driving him crazy was a silly thing, really. How many slices of bread are in this loaf?  Well, Peter wasn’t going to let himself be consumed by this problem for long, so he picked up the loaf, grabbed a knife, and proceeded to slice up the bread. “Aha”, he thought as he was finished slicing. There are 12 slices of bread in this loaf.

And that’s the iterative solution to the same problem.

### Which One is Better?

The gist of the recursive approach is that you set aside a piece of the problem and then look at the problem with this smaller domain. Each time you assess the problem it is smaller in scope, until it is a very simple matter.

If it takes so many words to describe the recursive way, why would you bother trying to approach a programming problem recursively?  The answer is in the previous paragraph: you break off a portion of the whole over and over again until you have reached the end and you are left with a trivial case that is easily resolved. Often that means that the recursive solution involves less code. Do you think I'm making that up? Let's move on to the algorithm we need for our project and you can see for yourself!

## Links

[Next](02-TheAlgorithm.md)

[The Little LISPer](https://mitpress.mit.edu/books/little-lisper-trade-edition)

[other links on recursion vs iteration]  

.pct 80
