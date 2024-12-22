## Data Structure and Intro
- What is a Data Structure (DS): a way of organizing data so that it can be used effectively
	- Essential ingredient in creating fast and powerful algos
	- Help manage and organize data
	- makes code cleaner and easier to understand
- Abastract Data Types vs Data Structures
	- Abstract Data type: is an abstraction of a data structure which provides onles the interface to which a DS must adhere to
	- interface does not give any specific details about how something should be implemented or in what programming language
	
| Abstraction | Implementation                                                |
| ----------- | ------------------------------------------------------------- |
| List        | Dynamic Array, linked List                                    |
| Queue       | Linked List based Queue, Array based queue, Stack based queue |
| Map         | Tree Map, HashMap / Hash Table                                |
| Vehicle     | Golf Cart, Bicycle                                            |
## Computational Complexity Analysis
- Two Questions we ask ourselves
	- Time needed for algo to finish
	- Space algo needs to compute
- Big-O notation gives an upper bound of the complexity in the worst case, helping to quantify performance as the input size becomes arbitrarily large
- O(n) is Linear Time
	- n is the size of the input
	- table below shows the complexity of big O ordered in smallest to largest

| Name              | Big-O         |
| ----------------- | ------------- |
| Constant Time     | O(1)          |
| Logarithmic Time  | O(log(n))     |
| Linear Time       | O(n)          |
| Linearithmic Time | O(nlog(n))    |
| Quadric Time      | O(n^2)        |
| Cubic Time        | O(n^3)        |
| Exponential Time  | O(b^n), b > 1 |
| Factorial Time:   | O(n!)         |
- Some examples of big O
	- O(n + c) = O(n)
	- O(cn) = O(n), c > 0
- Let f be a function that describes the running time of a particular algo for an input of size n:
	f(n) = 7log(n)^3 + 15n^2 + 2n^3 + 8
	The function above will just be O(f(n)) = O(n^3)  

## Static and Dynamic Arrays
- static array is a fixed length container containing n elements indexable from the range of 0 to n-1;
	- Indexable means that each slot/index in the array can be referenced with a number
- When and where to use it
	- Storing and accessing sequential data
	- Temporarily storing objects
	- used by IO routines as buffers
	- lookup tables and inverse lookup tables
	- can be used to return multiple values from a function
	- used in dynamic programming to cache answers to subproblems

Complexity

|           | Static array | Dynamic Array |
| --------- | ------------ | ------------- |
| Access    | O(1)         | O(1)          |
| Search    | O(n)         | O(n)          |
| Insertion | N/A          | O(n)          |
| Appending | N/A          | O(1)          |
| Deletion  | N/a          | O(n)          |

- Dynamic Arrays can grow and shrink in size as needed
- Implementing a dynamic array
	- create a static array with an initial capactiy
	- Add elements to the underlying static array, keeping track of the number of elements
	- If adding another element will exceed the capacity, then create a new static array with twice the capacity and copy the original elements into it

## Linked List
- Linked List is a sequential list of nodes that hold data which point to other nodes also containing data
- Where and When is it used
	- Used in many list, queue, and stack implementations
	- Great for creating circular lists
	- Can easily model real world objects such as trains
	- Used in separate chaining, which is present in certain hashtable implementations to deal with hashing collisions
	- often used in the implementation of adjacency lists for graphs
- Always maintain a reference to the head of the linekd list
- Parts of a linked list
	- HEAD: the first node in a linked list
	- TAIL: the last node in a linked list
	- Pointer: reference to another node
	- Node: An object containing data and pointer(s)
- Singly linked lists
	- only hold reference to the next node.
	- You always maintain a reference to the head of the linked lists and a reference to the tail node for quick additions/removals
- Doubly linked lists
	- Each node holds a reference to the next and previous node
	- Always maintain a reference to the head and the tail of the double linked list to do quick additions/removals from both ends of your list

| Type   | Pros                                     | cons                                   |
| ------ | ---------------------------------------- | -------------------------------------- |
| Singly | Uses less memory, simpler implementation | cannot easily access previous elements |
| doubly | can be traversed backwards               | takes 2x memory                        |

Complexity

| Actions          | Singly | Doubly |
| ---------------- | ------ | ------ |
| Search           | O(n)   | O(n)   |
| Insert at head   | O(1)   | O(1)   |
| Insert at tail   | O(1)   | O(1)   |
| Remove at head   | O(1)   | O(1)   |
| Remove at tail   | O(n)   | O(1)   |
| Remove in middle | O(n)   | O(n)   |


## Stack
- Stack is a one-ended linear data structure which models a real world stack by having two primary operations, namely push and pop
	- LIFO (LAST IN FIRST OUT)
- When and where is a stack used
	- Used by undo mechanisms in text editors
	- Used in compiler syntax checking for matching brackets and braces
	- can be used to model a pile of books or plates
	- used behind the scenes to support recursion by keeping track of previous function calls
	- can be used to do a depth first search (DFS) on a graph

Complexity 

| Actions   | Big O |
| --------- | ----- |
| Pushing   | O(1)  |
| Popping   | O(1)  |
| Peeking   | O(1)  |
| Searching | O(n)  |
| Size      | O(1)  |

## Queue
- Queue is a linear data structure which models real world queues by having two primary operations, namely enqueue and dequeue
- Enqueue = adding = offering
- Dequeue = polling
- When and Where is it used
	- any waiting line models a queue
	- can be used to efficiently keep track of the x most recently added elements
	- web server request management where you want first come first serve
	- breadth first search (BFS) graph traversal
- 

Complexity

| Actions  | Big O |
| -------- | ----- |
| Enqueue  | O(1)  |
| Dequeue  | O(1)  |
| Peeking  | O(1)  |
| Contains | O(n)  |
| Removal  | O(n)  |
| Is Empty | O(1)  |


## Priority Queue