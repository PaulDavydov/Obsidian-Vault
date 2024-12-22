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
- 