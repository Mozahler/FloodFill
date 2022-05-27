## The Algorithm

Now I want to discuss the focus of this demo project: the `Flood` algorithm (as a View on an iOS device).

Given a grid of different colors, select a color then tap on a square of any different color. The cell you tapped should change to the selected color. Any adjacent cell (directly above or beside this one) having the same color as the square you just tapped (before you changed id) should also change to the new color. If it changed, then any of its neighbors need to undergo the same scrutiny and transformation if applicable.

This simple algorithm can be useful in a drawing app where you want to modify the color of a portion of your image.

Here’s one possible (recursive) solution to the problem:

```swift
static func recursiveAlgorithm(targetFill fillValue: Int, in grid: inout [[CellItem]], at point: (x: Int, y: Int), originalFill: Int? = nil) -> [[CellItem]] {
   /// make sure the point is on the board (or we're done)
   guard isValidPlacement(point) else { return grid }
   /// the first time this is called we don't have `originalFill`
   /// so we read it from the starting point
   let startValue = originalFill ?? grid[point.x][point.y].value

   if grid[point.x][point.y].value == startValue {
      grid[point.x][point.y].value = fillValue
      _ = recursiveAlgorithm(targetFill: fillValue, in: &grid, at: (point.x, point.y - 1), originalFill: startValue)
      _ = recursiveAlgorithm(targetFill: fillValue, in: &grid, at: (point.x, point.y + 1), originalFill: startValue)
      _ = recursiveAlgorithm(targetFill: fillValue, in: &grid, at: (point.x - 1, point.y), originalFill: startValue)
      _ = recursiveAlgorithm(targetFill: fillValue, in: &grid, at: (point.x + 1, point.y), originalFill: startValue)
   }
   return grid
}
```
As you can see, it’s not a lot of code (a dozen lines plus comments). 
That’s the beauty of a recursive approach.

We start by making sure that the cell we are looking at is on the board. If we clicked on the top row, then the cell neighboring above that cell doesn't exist. Similarly the left, right and bottom edges are missing some of their neighbors. We can return now if the cell isn't even on the board. No harm, no foul.

The first time we call this method we know what color we want to change the square(s) to, but we don't know what we will be changing it `from`. That's an important part of the algorithm, and trivial to realize. The color of the cell tapped is the color we will be interested in. 

If we tap on a blue cell, we process it, then the neighboring cells will be checked to see if they are blue as well. If they are, we repeat the process on each of them in turn (change the color, check for eligible neighbors.) If this neighbor is not of the same original color, we proceed to the next neighbor of the tapped cell and see if it is blue.

```
let startValue = originalFill ?? grid[point.x][point.y].value
```

The first time this is called, `originalFill` is nil. So the `startValue` is initialized here with whatever color is in the cell that was tapped.

Next we check to see if the cell is the color we want to change it to. If it is, then we're all done and we return the grid.

If it's not, then we change the cell to the new color, and then we try each potential neighbor (remember we might not have cells on all four sides, but our code will ignore the outliers).
Notice that in our recursive call we now have startingValue - blue in our example - and that color is passed as the `originalFill` value.


Here’s a solution using iteration:

```swift
   /// Flood fill - this is called once per tap on any tile
   /// the binding is required to properly animate the color changes sequentially
   /// the system would prefer to animate the entire sequence as one unit
   func algorithm(targetFill: Int, in grid: Binding<[[CellItem]]>, at point: (x: Int, y: Int))  {
      /// this is the color value under the cursor when first tapped
      let originalFill: Int = grid[point.x][point.y].value.wrappedValue
      var squaresToFill:  [(x: Int, y: Int)] = [point]
      var counter: TimeInterval = 0.0

      /// does not update any cells, it just adds valid ones to the queue
      func tryAddMove(_ move: (x: Int, y: Int), wait: inout TimeInterval) {
         /// make sure the point is on the board (or return)
         guard isValidPlacement(move) else { return }
         // only update points with values that match the original value
         guard grid[move.x][move.y].value.wrappedValue == originalFill else { return }
         /// add it to the stack
         squaresToFill.append(move)
      }

      func neighboringSquares(_ square: (x: Int, y: Int)) {
         var wait: TimeInterval = 0.0
         /// each of these calls adds the cell to the stack for processing if its color matches the original swap in value
         tryAddMove((x: square.x, y: square.y - 1), wait: &wait)
         tryAddMove((x: square.x, y: square.y + 1), wait: &wait)
         tryAddMove((x: square.x - 1, y: square.y), wait: &wait)
         tryAddMove((x: square.x + 1, y: square.y), wait: &wait)
      }

      /// this will only stop if nothing changed
      /// i.e.: -- we didn't add another square after using the last one in the stack
      /// (if tryAddMove didn't add to the stack and it was called with only one item)
      while let square = squaresToFill.popLast() {
         /// if it's already the new color, don't visit, move to next square in stack
         guard grid[square.x][square.y].value.wrappedValue != targetFill else { continue }

         withAnimation(.linear(duration: boardConfig.tempo).delay(counter)) {
            /// This is the only place where the color is changed - and it's animated.
            grid[square.x][square.y].value.wrappedValue = targetFill
         }
         counter += boardConfig.tempo
         neighboringSquares(square)
      }
   }
}
```

Notice this takes considerably more code than the recursive version. In fact, it has more than twice as many lines of code.
Which one is easier to understand just by reading the code? Your answer may vary from mine.  

With both approaches the process is the same:

Make sure the current move is a valid one, and if the color needs changing, change it then check the neighbors, otherwise repeat until there are no more changes.

### Stacks and Memory

One downside to a recursive approach (although not really applicable in such a tiny domain as our slice of bread or 14 x 14 matrix examples) is that since we are only solving a portion of the whole at a time, we need to set aside our partial result and calculations while we recurse and continue breaking things down. 

In a computer program, this means setting aside memory in a stack (a container where you add things to the top, and you take things off the top when you need them - this process can be called FIFO or First In First Out). What if your input dataset is huge, and solving the problem involved 150,000 recursive calls?  That’s a lot of memory to set aside.

I like to use a recursive approach when possible, so you may be surprised to see that the game in this app is solved using the iterative approach. Although the recursive approach shown above is perfectly serviceable, my command of the current SwiftUI animation system doesn't let me produce the results I want. So I'll come back to that some other time.

I hope by comparing and contrasting the two approaches the abstraction of the original algorithm is less abstract at this point.

## Links

[Next](./03-ProtocolsAndProtocolConformance.md)

recursion links belong in previous chapter

need other topics to make the unit less monolithic

.pct 60


