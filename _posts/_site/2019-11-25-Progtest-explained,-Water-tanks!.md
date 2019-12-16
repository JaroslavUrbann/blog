## Problem description
The task is to write a program that can compute the altitude of the water surface in a system of water tanks.

Let's say that there is a water company that manages water tanks. Every water tank has a block-like shape and is connected to every other water tank with tubes of zero volume (to ease our calculations). The situation is as given:

![Image from the original Progtest problem](https://raw.githubusercontent.com/JaroslavUrbann/blog/master/assets/images/water_tanks.png)
<br/><br/>
*Image from the original Progtest problem*

The input is a set of n (n <= 200 000) water tanks defined as Alt H W D. Alt is the altitude of the bottom of a given water tank, H is the height, W is the width and D is the depth.

Next comes a sequence of queries with different volumes of water. **The task is to compute the altitude of the water surface**.
<p>&nbsp;</p>

## The solution

### 1) Cultivating our data

Firstly, the width and depth of a water tank can be multiplied and combined into a single variable containing the area of a base of a given water tank.

Secondly, the altitude and height of a water tank can also be combined into (semantically) one variable, the altitude of the lower base (Alt) and altitude of the upper base (Alt + H).

We now have **2 types of variables:** altitudes and areas.

### 2) The imagination bit

Now imagine going from the lowest point of our water tank system to the highest point of our water tank system. The total water area would change based on our water tanks being "added" and "removed" as we go.

We should try and represent that with our data.

### 3) Creating our dataset

Notice that our input data can be easily transformed into 2n objects with the following two attributes: area_added and altitude. Let's call these objects thresholds and look at the example below:

![Visualizing Thresholds](https://raw.githubusercontent.com/JaroslavUrbann/blog/master/assets/images/thresholds.png)
*Note the minuses in area_added in the top thresholds.*


If we save our thresholds in an array and sort them by altitude, we can imagine our area_added attributes looking like this:

![Our array](https://raw.githubusercontent.com/JaroslavUrbann/blog/master/assets/images/arr1.png)

### 4) Transformations, transformations

If we iterate over the array once and add up our surfaces, we can visualize the state of our array like this, aka **the total surface at each altitude**:

![Our array after the first transformation](https://raw.githubusercontent.com/JaroslavUrbann/blog/master/assets/images/arr2.png)

If we now take an item from the array and multiply its area with (altitude of the next item - our items' altitude), **we get the total volume between 2 altitudes**. Since we want to query the global total volume, we just need to iterate the array once more and take these intermediate volumes and add them all together into an array that's telling us the cumulative volume of our water tank system.

After these transformations, we get an array with **the cumulative volume of our water tank system.**


Remember, our now array stores 2 types of information at each index, the altitude and the total volume of our system up to that altitude.

So we can now perform a simple binary search on our array to get upper and lower altitude boundaries for our volume and calculate the real altitude with:

<a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\\&space;A_L&space;=&space;\text{Altitude&space;of&space;the&space;lower&space;boundary}\\&space;A_U&space;=&space;\text{Altitude&space;of&space;the&space;upper&space;boundary}\\&space;V_Q&space;=&space;\text{Queried&space;volume}\\&space;V_L&space;=&space;\text{Volume&space;of&space;the&space;lower&space;boundry}\\&space;V_U&space;=&space;\text{Volume&space;of&space;the&space;upper&space;boundry}\\&space;\\&space;V&space;=&space;A_L&space;&plus;&space;(A_U&space;&plus;&space;A_L)&space;\cdot&space;\tfrac{V_Q&space;-&space;V_L}{V_U&space;-&space;V_L}\\" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\large&space;\\&space;A_L&space;=&space;\text{Altitude&space;of&space;the&space;lower&space;boundary}\\&space;A_U&space;=&space;\text{Altitude&space;of&space;the&space;upper&space;boundary}\\&space;V_Q&space;=&space;\text{Queried&space;volume}\\&space;V_L&space;=&space;\text{Volume&space;of&space;the&space;lower&space;boundry}\\&space;V_U&space;=&space;\text{Volume&space;of&space;the&space;upper&space;boundry}\\&space;\\&space;V&space;=&space;A_L&space;&plus;&space;(A_U&space;&plus;&space;A_L)&space;\cdot&space;\tfrac{V_Q&space;-&space;V_L}{V_U&space;-&space;V_L}\\" title="\large \\ A_L = \text{Altitude of the lower boundary}\\ A_U = \text{Altitude of the upper boundary}\\ V_Q = \text{Queried volume}\\ V_L = \text{Volume of the lower boundry}\\ V_U = \text{Volume of the upper boundry}\\ \\ V = A_L + (A_U + A_L) \cdot \tfrac{V_Q - V_L}{V_U - V_L}\\" /></a>