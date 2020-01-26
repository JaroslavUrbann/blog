
## Problem description
Kakuro is a Japanese mashup of Sudoku and crossword puzzles. It is composed of multiple entries similar to crossword puzzles, but instead of you having to input letters that compose a certain word, you instead input numbers that add up to a given sum. The twist is that a number can't be repeated in a given entry. Just like in Sudoku.
The goal is to create a solver that can quickly count the number of possible solutions to a given Kakuro puzzle.

## Solution
In short, my solution is a backtracking algorithm that temporally sets one of the possible values to an unsolved cell and then applies a series of deduction rules. It then goes to another undetermined cell and does the same thing until it arrives at a contradiction or solves the puzzle. It then goes back one step and sets a different value to the last cell it went to.

### Deduction steps
![The recursive application of deduction steps](https://raw.githubusercontent.com/JaroslavUrbann/blog/master/assets/images/kakuro_explained.png)*The recursive nature of the update rules summarized with an image*
<p>&nbsp;</p>

Each entry can have multiple sets of solutions, for example, 9 in 3 := [126] or [135] or [234]. Each cell of this entry has by default the possible values of [123456], which is the union of these sets.
Whenever the algorithm assigns a unique value to a cell, it checks whether it can remove any sets from the entries the cell belongs to. For example, if the algorithm sets "1" as the value of a cell belonging to a 9 in 3 entry, it can remove the set [234]. If the union of these sets then becomes different (in this case [123456] -> [12356]), the algorithm goes through all the cells of the given entry and recalculates the possible values that the cells can take.
The possible values of a cell are calculated by doing an intersection of 4 values;

1) union of the sets of the vertical entry  
2) union of the sets of the horizontal entry  
3) mask of determined values of the vertical entry  
4) mask of determined values of the horizontal entry  

The masks of determined values make sure that a cell can't take a value that was already set to a cell in the same entry. These masks are being updated at step (1) along with the total sum that the filled cells add up to so far.
If the updated cell can take only one possible value, (1) gets called for that particular cell.
Otherwise, if the cell's possible values change, but it can still take multiple values, (2) gets called for the other entry of the cell.
Because of these last 2 steps, this "deduction" part of the algorithm has a chain-like effect whereby updating one cell changes other cells as well, but only those that need to be updated and it does so without any unnecessary overhead.

### The algorithm summary
First, the possible values of each cell are calculated. Then, for any cells that can take only one value, (1) is called, thereby also updating the relevant adjacent cells thanks to the recursive nature of the deductive algorithm.
Secondly, the backtracking algorithm starts recursively stepping through the cells row by row and whenever it stumbles upon a cell that isn't solved, it sets the cell to one of its' possible values and calls (1). It then continues until the Kakuro is solved or the algorithm arrives at a contradiction, which could be one of two things; either all the cells of an entry have been filled but the sum doesn't add up to the required value or a cell has no possible value it can take. Then the algorithm goes back (or "backtracks") to the last guessed cell and fills in another one of the possible values that it can take.
At each level of the recursion, there is an empty backup array created where all the cells that are meant to be updated are saved. This way these updated cells can be restored to their original value as if nothing happened before the algorithm tries to fill in another one of the possible values.
