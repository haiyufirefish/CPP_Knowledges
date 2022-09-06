There are only two ways to store data structures: arrays (sequential storage) and linked lists (chained storage).

How do you understand this sentence? Isn't there also various data structures such as hash tables, stacks, queues, heaps, trees, graphs, etc.?

When we analyze problems, we must have recursive thinking, from top to bottom, from abstract to concrete. That's all you have to list. Those are all "superstructures", while arrays and linked lists are the "structural basis". Because those diverse data structures, their origins are all special operations on linked lists or arrays, and the APIs are different.

For example, the two data structures "queue" and "stack" can be implemented using either linked lists or arrays. Implemented with an array, it is necessary to deal with the problem of expansion and contraction; implemented with a linked list, there is no such problem, but more memory space is needed to store the node pointer.

There are two ways to represent a "graph", an adjacency list is a linked list, and an adjacency matrix is a two-dimensional array. The adjacency matrix judges the connectivity quickly, and can perform matrix operations to solve some problems, but it is very space-consuming if the graph is sparse. The adjacency list saves space, but many operations are definitely not as efficient as the adjacency matrix.

A "hash table" is a hash function that maps keys into a large array. And for the method of solving hash collisions, the zipper method requires linked list characteristics, which is simple to operate, but requires additional space to store pointers; the linear exploration method requires array characteristics for continuous addressing, and does not require pointer storage space, but the operation is slightly more complicated. some.

"Tree", implemented with an array is a "heap", because a "heap" is a complete binary tree, using an array to store no node pointers, and the operation is relatively simple; implementing a linked list is a very common kind of "tree", because it is not necessarily It is a complete binary tree, so it is not suitable for array storage. For this reason, on top of this linked list "tree" structure, various ingenious designs are derived, such as binary search tree, AVL tree, red-black tree, interval tree, B tree, etc., to deal with different problems.

Friends who know Redis database may also know that Redis provides several common data structures such as lists, strings, sets, etc., but for each data structure, there are at least two underlying storage methods, so as to store data according to the actual situation Use an appropriate storage method.

In summary, there are many types of data structures, and you can even invent your own data structures, but the underlying storage is nothing but an array or a linked list. The advantages and disadvantages of the two are as follows:

Because the array is compact and contiguous, it can be accessed randomly, and the corresponding element can be quickly found through the index, and the storage space is relatively saved. But just because of continuous storage, the memory space must be allocated at one time, so if the array needs to expand, it needs to reallocate a larger space, and then copy all the data, the time complexity is O(N); Insertion and deletion are performed in the middle of the array, and all the following data must be moved each time to maintain continuity, and the time complexity is O(N).

Because the elements of the linked list are not continuous, but rely on the pointer to point to the position of the next element, so there is no expansion of the array; if you know the predecessor and the successor of an element, you can delete the element or insert a new element by operating the pointer, and the time is complicated. Degree O(1). But just because the storage space is discontinuous, you cannot calculate the address of the corresponding element based on an index, so it cannot be accessed randomly; and because each element must store a pointer to the position of the preceding and following elements, it will consume relatively more storage space.



By the way, the "algorithm" made by the "algorithm engineer" is completely different from the "algorithm" in "Data Structures and Algorithms", so as to avoid misunderstanding by some beginners.

For the former, the focus is on mathematical modeling and parameter tuning experience, and computers are really just tools for computing; while the latter focuses on computer thinking, which requires you to be able to abstract and simplify practical problems from the perspective of a computer. , and then use a reasonable data structure to solve the problem.

Therefore, you must not think that you can become an algorithm engineer if you learn data structures and algorithms, and don’t think that you don’t need to learn data structures and algorithms as long as you don’t become an algorithm engineer. Frankly speaking, most of the development jobs are based on ready-made development frameworks, and you rarely encounter problems related to underlying data structures and algorithms, but another fact is that as long as you are looking for technology-related positions, data structures and The investigation of the algorithm is inescapable, because this knowledge point is recognized as the basic skill of programmers.



**There are two key difficulties in exhaustiveness: no omission and no redundancy.**