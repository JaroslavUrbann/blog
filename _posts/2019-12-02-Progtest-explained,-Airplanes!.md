## Problem description
The task is to write a program that outputs pairs of airplanes that are in danger of collision. &nbsp;

Each airplane (let's just call them points) has two coordinates [_x_, _y_] and a name. Your goal is to write out the shortest distance that can be found between these points and the names of the points that are separated by that distance. &nbsp;

The input & output could look something like this:
 >**Input:**  
 >[0, 0] Airplane1  
 >[5, 5] Airplane2  
 >[3, 0] Airplane3  
 >[8, 5] Airplane4  
 
 >**Output:**  
 >Shortest distance: 3  
 >Airplane1 - Airplane3  
 >Airplane2 - Airplane4  
 
 &nbsp;
## The solution
&nbsp;
### 1) Designing the algorithm

For our solution, we'll be using a divide and conquer approach as described [here](https://en.wikipedia.org/wiki/Closest_pair_of_points_problem)

The quick summary goes like this: &nbsp;

we take our points and split them in half: &nbsp;
![points_divided_in_half](https://raw.githubusercontent.com/JaroslavUrbann/blog/master/assets/images/points1.png)&nbsp;
we calculate the shortest distance between 2 points in each half:
![points_with_distance](https://raw.githubusercontent.com/JaroslavUrbann/blog/master/assets/images/points2.png)&nbsp;
we check if there is a pair of points in closer proximity to each other than the distance that we already found, on the edge of these 2 groups:
![checking_the_strip](https://raw.githubusercontent.com/JaroslavUrbann/blog/master/assets/images/points3.png)&nbsp;
### 2) Things to note:
**a)** The x coordinate at which we split our points is the x coordinate of a point at the (n/2)-th index when our points are sorted by x. &nbsp;

**b)** Calculating the shortest distance in the left and right group respectively is done recursively by the same algorithm until there are only around 3 - 4 points left and then we just do a brute force search. &nbsp;

**c)** Splitting the points and calculating the shortest distance in each group takes O(log N) time. &nbsp;

**d)** Calculating the shortest distance on the edges of our groups takes O(n) time, here's why: &nbsp;

When traversing the array of points that are located in the strip shown above (going from lowest to highest), the shortest distance can only occur between points that are separated by less than 8 points of each other when sorted by Y. Look at this visualization below:
![strip_zoomed_in](https://raw.githubusercontent.com/JaroslavUrbann/blog/master/assets/images/points4.png)
Imagine drawing a rectangle like this for every point you visit when traversing the array. You can't fit more than 8 points in it, because if you could, the distance *d* would be smaller since it represents the shortest distance between 2 points on each side. &nbsp;

So since we're checking only up to 8 points for each point, the complexity is O(n). &nbsp;

**e)** Deriving from the observations above, at each step of our recursive algorithm, we'll need to have our points saved twice,  ordered by *x* in one array (to split them into 2 groups) and ordered by *y* in another array (to traverse the strip). &nbsp;

### 3) So, why won't this work out of the box?
&nbsp;
The reason is, that **the algorithm above assumes unique *x* coordinates** for each point. Meaning,  2 points can't share the same *x* coordinate, which isn't the case in our problem. &nbsp;

So how can this go wrong? &nbsp;

Well, your sorting will most definitely fail. Imagine an input of points with *x* values like this: &nbsp;

1 2 3 4 5 5 5 5 5 5 5 5 &nbsp;

You pick the middle point (5), and split the array that is ordered by *Y* into two groups: points with *X* that is smaller than 5 and points with *X* bigger or equal to 5 and get 2 groups: &nbsp;

1 2 3 4 
and 
5 5 5 5 5 5 5 5 &nbsp;

And from this point onwards, you won't ever split the fives apart meaning that your algorithm will never stop. &nbsp;

### 4) How can we fix this then?

To fix this, we are going to choose more than one "middle point" when splitting our points into 2 groups and we won't be including them in neither of these groups. More precisely, we are going to choose all points that have the same *X*  as the point at the  (n/2)-th index, as our middle points. &nbsp;

After that, we will calculate the shortest distance in each group and then traverse the strip with our middle points included. &nbsp;

### 5) It still doesn't work, what's wrong?

The problem that is most likely arising here is, that the 8 point rule described above doesn't hold anymore. If we get an input with points that have the same *X* and *Y* and they end up as our middle points, then there can be an infinite amount of points in our rectangle that is visualized above. &nbsp;

To mitigate this, we just simply allocate another dynamic array to hold the indexes of all points that are less than *d* (the shortest distance that we found so far) below our current point, when we traverse the split. &nbsp;

With all that, our algorithm should now be modified enough to work on this particular problem.
