## Intelligent Stats (Guru)

I made this question after writing the advanced problem 'Infinite Degrees of Separation', so when I made this question I was too caught 
up in thinking about the graphs from advanced. I somehow had it in my mind that I had enforced every undirected cycle to be in the same 
direction, like so:

```
O<----O
|     ^
|     |
v     |
O---->O
```

But, of course the question allows for undirected cycles such as these:

```
O---->O
|     |
|     |
v     v
O---->O
```

If I had made it the first case, the problem would have been reducible to a Dynamic Programming problem that should be solvable on 
N = 100,000. I won't explain this because maybe I'll try this again later (it's actually related to The Dark's solution).

However, since that was not the case, after finding out that there's no current way to efficiently solve N = 100,000 I reduced it to 
10,000.


## Algorithm

With N = 10,000, this is no longer a "Guru" problem, and actually pretty straight-forward. 

A 'node' is a person, and an 'edge' is an arrow that connects to anyone he/she has won against. 
The resulting network is called a 'graph'.

To count the number of people a node 'can beat', we count how many nodes are 'reachable' by following the edges in the graph from said 
node.

To do this, first store the list of neighbours for every node, and then run a search once from every node. 
The search counts the number of nodes it can recursively reach (aka 'descendents') and returns that answer.

This wasn't supposed to be tricky. A search from a node (a DFS) in a connected graph takes O(V+E) time, where V and E are a maximum of 
10,001 and 10,000 respectively. So doing this search for every node will take roughly 100 million operations. 
That may sound like a lot, but if we're careful with how we write it, this will run in under 3 seconds on a decent computer.

There are two ways we can be careful:
Have efficient lookup and storage of names.
Watch your memory allocation!


## Efficient Lookup and Storage of Names

What we want to do is associate every name with its own distinct integer. That way we no longer need to worry about names-- 
only the integer that represents a name.

The problem is we can't just say the i'th name in the file is associated with the i'th integer, because names are arbitrarily repeated 
throughout a file, and we need to recognize that the first integer mapping for a name lasts for all future occurrences of that name in 
the file.

There are many ways we can do this. I've come up with 4 varying methods:
Use the STL <map> in C++ to bind strings to integers.
Read in the whole file first. Sort the names in alphabetical order. Remove duplicates. A name's associated integer is its position in 
the array.
Write a Hash Table to bind strings to integers.
Write a Trie to bind strings to integers.

The first method is what The Dark used. Maps in C++ are implemented with Red-Black trees, and so each of the n operations takes 
logn time. Using a map on huge blocks of data can be slow because of the memory allocation that occurs. Only use a map when you're 
confident that the rest of your code is fast enough. The payoff is great, with the shortest and simplest code.

The second method is asymptotically as fast as the first method (sorting takes nlogn time). However, since there's no dynamic 
allocation, and the conversion is all isolated in a couple for loops, the compiler can make many optimizations and this turns out to be
much faster.

The third method is even faster. It's not hard to write a Hash Table with a single lookup operation and a fixed size 
(20000 names is the maximum).

The fourth method is the fastest possible method. The time it takes to lookup and store an integer for a string is constant per letter! 
It's also very easy to write. You create a tree that branches to represent all possible alphanumeric strings of length 20 
(so a tree with 62<sup>20</sup> nodes). But 62<sup>20</sup> is far too large, so we only create the nodes as they are looked at and needed. 
Each word has its own path down the tree, and thus its own terminating vertex. 
The terminating vertex is used to hold the associated integer for that string.

This method is very memory inefficient for randomized data, but 128 MB of memory is plenty and it's so much easier to write and so much 
cooler.


## Watch Your Memory Allocation!

Memory allocation is fine until you are allocating and deallocating large blocks of 10000+ bytes repeatedly. 
This really starts to take a huge toll on your run-time.

If we were to recursively DFS for every node, we could potentially allocate 10000 stack frames 10000 times. T
he amount of allocations is then comparable to the number of operations in the algorithm itself. That's very bad!!

To fix this, we can allocate a queue once and use the same memory over and over in a Breadth-First Search.

This is also convenient because the length of your queue after a breadth-first search will be the number of nodes it could reach!
