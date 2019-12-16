![Get your axes out](https://raw.githubusercontent.com/JaroslavUrbann/blog/master/assets/images/chopping_wood.gif)
## Problem description
The task is to write a program that effectively cuts boards into smaller pieces for transportation.
<p>&nbsp;</p>

You get a board of a given size and your task is to use the lowest number of cuts possible to split it into smaller boards. These smaller boards need to satisfy a maximum area constraint given to you in the input. Additionally, the sizes of the final boards can't be in a ratio larger than 2:1 or smaller than 1:2.

#### The input & output could look something like this:
<img src="https://raw.githubusercontent.com/JaroslavUrbann/blog/master/assets/images/Selection_029.png" alt="An example input" height="400"/>
 

## The solution

This task was designed to teach students recursion and so the bulk of the difficulty is hidden in the implementation itself, rather than the algorithm. With that, I'll just quickly go through the algorithm:
<p>&nbsp;</p>

### 1) Realizing that there are no algorithmic shortcuts
After spending a few minutes with your pen and paper thinking about this problem, you should conclude that there is no general rule to be found in this particular problem. You'll realize that for every pattern you'll find, you can find a counter-example. And if you can't, Progtest can.
Brute-force is the only option making this an algorithmically boring problem.
<p>&nbsp;</p>

### 2) A simple binary tree structure
If a board is cut, it has to be split into 2 pieces. Each of these pieces is then a board in itself. Intuitively, this creates a binary tree-like structure where each node has 2 children like this:
>typedef struct BOARD {  
>int width, height;  
>BOARD *child1, *child2;  
>} board;  
<p>&nbsp;</p>

### 3) The recursive function
As I said, there isn't anything to this problem other than just understanding recursion. The whole program can be stripped down to one recursive function that could work something like this:


>int cutBoard ( board, max_area )  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;if ( area of the board < max_area && 1:2 < ratio < 2:1)  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return 0  
><p>&nbsp;</p>
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;number_of_cuts = INT_MAX  
><p>&nbsp;</p>
><p>&nbsp;</p>
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;child1.width = width / 2  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;child2.width = width - child1.width  
><p>&nbsp;</p>
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;while ( child1.width > 0 )  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cuts1 = cutBoard ( child1, max_area )  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cuts2 = cutBoard ( child2, max_area )  
><p>&nbsp;</p>
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;if ( cuts1 + cuts2 + 1 < number_of_cuts )  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;number_of_cuts = cuts1 + cuts2 + 1  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;board.child1 = child1  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;board.child2 = child2  
><p>&nbsp;</p>
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;child1.width --   
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;child2.width ++  
><p>&nbsp;</p>
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;** do the same for height **  
><p>&nbsp;</p>
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return number_of_cuts  


### 4) A simple optimization
As this program would be incredibly slow by default, there **needs** to be an optimization possible somehow.
The one I opted for is very simple. You just allocate a 2D array that is about the size of your input board. Each item in the array has 2 attributes: number_of_cuts and board.
Now you just cache all the values you get. This speeds things up immersely since you never need to recalculate anything.
<p>&nbsp;</p>
Note that **this optimization can be easily "broken"** just by inputting large values (for example Size: 1000 x 1000, Maximum Area: 500 000, requires just one cut but allocates a large amount of memory). But for this particular problem, it is sufficient.
