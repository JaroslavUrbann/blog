
## Problem description
The task is to write a program that suggests common phrases based on a given text.
<p>&nbsp;</p>

The input contains a set of phrases with usage frequencies and a set of queries. Your goal is to output up to 50 of the most common phrases that contain a given queried text. This should be done for every query.
<p>&nbsp;</p>

The input & output could look something like this:
 >**Input:**  
**Common phrases:**
30:Qui dolorem ipsum quia dolor sit amet
70:magnam aliquam quaerat
40:torquent per conubia nostra
90:tellus id magna elementum
**Queries:**
agna
dolor
 
 >**Output:**  
**Found: 2**
tellus id magna elementum
magnam aliquam quaerat
**Found: 1**
Qui dolorem ipsum quia dolor sit amet
<p>&nbsp;</p>

## The solution
### 1) A few considerable approaches

Since this isn't a new problem by any means, there is a handful of approaches one can go with. Most notably, suffix trees and suffix arrays.
<p>&nbsp;</p>
A suffix tree can be constructed in O(n) time, where n denotes the number of characters in a phrase, and it can answer queries in O(m) time, where m denotes the number of characters in a query. As I see it, the two biggest disadvantages of suffix trees are memory consumption (which shouldn't be a problem as we were promptly informed in the original problem description) and secondly the complexity of creating the actual tree in O(n) time. It isn't by any means a trivial algorithm, especially for first-year students.
<p>&nbsp;</p>
A suffix array can be constructed in O(n * log n) at best, if you don't want to build a suffix tree in O(n) time beforehand. It can answer queries in O(m * log n) in the worst case. Suffix arrays are much easier to construct and take up less space. On the other hand, they do seem much slower, compared to suffix trees, when you look at the time complexities.
<p>&nbsp;</p>

### 2) Why didn't it work when I implemented one of these algorithms?

Well, we already established the importance of good time complexity in regards to the number of characters in a phrase and the number of characters in a query, but there is one more thing we left out to consider. It's the number of phrases we have to search through.
<p>&nbsp;</p>
You might have thought about this and concluded that there is no reasonable way to include all phrases in one big suffix tree or one big suffix array because the worst-case time complexities would be insane. And you're right, but this is where you made the mistake of not considering the type of data you are being given by Progtest.
<p>&nbsp;</p>

### 3) Clues, and why this task wouldn't be doable without them
The point to be taken here is, that with different types of input, the problems' shape drastically changes.
The only description of the input for the bonus test went something like this: "There will be many phrases, they will have many characters and there will be many queries".
What you need to realize is, that this doesn't tell you absolutely anything. The way you design your algorithm should change based on if you'll be getting 100 phrases that all look something like this:
>aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa

or if you'll be getting 10 000 phrases that all look something like this:
>abcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcd

and so on...
<p>&nbsp;</p>
So in my opinion, the right move to make here is to take a clue for the bonus test and figure out what is the structure of the data you'll be getting.
 <p>&nbsp;</p>
 
### 4) I took the clue, what now?

If your clue looks anything like mine, then you should be able to deduce that each phrase will have about 160 characters, that there can be up to about 5000 phrases and that each query will have about 45 characters. And most importantly, that these phrases will be reasonable, meaning that there won't be any phrases with one repeating letter or any [fibonacci words](https://en.wikipedia.org/wiki/Fibonacci_word), etc.
<p>&nbsp;</p>
Taking the figures above, you can easily calculate that with 160 and 45 characters, the difference between a suffix tree and a suffix array will most likely not be noticeable. Both in search and in construction. What will be noticeable is the **possible multiply factor of 5000 for each query**.

### 5) So what's the actual algorithm I should use?

I believe that you can use any reasonable algorithm as long as you optimize it in accordance with the Y-axis of the input data, meaning the number of phrases.
I ended up just creating one big suffix array for all the phrases altogether.
#### Here's a quick summary of how I create my data structure:
**a)** Allocate an array for **n * l** suffixes, where **n** stands for the number of phrases and **l** stands for the average length of a phrase
<p>&nbsp;</p>

**b)** Each suffix has these attributes:
- Pointer to a phrase  
- Starting index (Where the suffix starts in the phrase)
<p>&nbsp;</p>

**c)** Fill the array by iterating once through all the phrases
<p>&nbsp;</p>

**d)** Sort the array. I'm literally just using quicksort with no additional tricks. This could take up to about O{ (n * l^2^) * log (n * l^2^) } because we have n * l items and we could need to compare up to l characters when comparing two suffixes. But that's only if all the phrases were composed of just one repeating letter. Since we know that isn't the case, quicksort will most likely need to compare only about the first 3-4 characters when comparing two suffixes. So the sorting should be reasonably fast.
<p>&nbsp;</p>

#### And a quick summary of how I do searches in my suffix array:
**a)** Do a binary search on the suffix array and either return that no matches were found or return the position of a match.
<p>&nbsp;</p>

**b)** Check for other matching suffixes around the one that was found and keep track of the phrases they belong to.
<p>&nbsp;</p>

**c)** Take these phrases and print out up to 50 of the most common ones.
<p>&nbsp;</p>

And that's it!
