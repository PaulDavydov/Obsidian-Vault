## Core Java Concepts

1. What is the difference between JDK and JRE?
	- The JDK is the development kit of the Java language. It contains the JRE as well. Biggest difference is the JDK is meant to develop code vs JRE is meant to run it
2. Why is Java a platform independent language?
	- It is a platform independent language, on the condition that if the platform can run the JRE, otherwise it will not be able to.
3. What is the difference between an abstract class and an interface?
	- Interface can view it as a contract or a promise that a class will do/have. Essentially ensures that the implementing class will take the methods the interface provides, and adds some logic to them. 
	- Abstract class is a basis for different subclasses that share behavior which does not need to be repeatedly created. Subclasses must complete the behavior and have the option to override the predefined behavior.
4. What is the difference between final, finally, and finalize?
	- Final is used in Java to declare entities in our code, that cannot be later modified. It applies to variables, methods, and classes
		- When intializing a final variable, it must only be intitiazlied once. Any changes to it will cause a compile-time error
		- When initializinng a final method, these methods cannot be overridden in any subclass. Useful when you need to implement a consistent method across a variety of subclasses
		- Final class means that the class cannot be subclassed. Helps create a fully immutable object, that will define certain behaviors that cannot be changed
	- Finally is used during instances of Java exception handling. Usually added to the end of a try-catch block, making sure to run code regardless if a exception was thrown or caught.
	- Finalize method is called by the garbage collector, when it determines that there are no more references to the object. Can be overriden by objects
5. What is the difference between stack and heap memory?
	- The difference being when the allocation of memory happens. For stack memory it happens during the function call stack. the size of the memory is known to the compiler
	- heap memory is allocated during the execution of instructions written by developers. Everytime we create an object, it is stored in the heap space, while stack contains the referencing information of the object
6. What is the difference between method overloading and method overriding?
	- Overloading happens when two or more methods are created in a object. The difference being, is that one may ask for more parameters than the other. compile time
	- Overriding occurs when inheriting a class from a interface or another object. Essentially we take the method and implement our own logic into it. Though the name, return type, and paramters must be the same. run time
7. What is the difference between a private and a protected modifier?
	- Private entails only the class it is implemented on, can modify/access it
	- protected means the same package or subclass in the a different package can access this method
	- public means that anyone can access it
	- default means that only classes in the same package can access it
8. What is constructor overloading in Java?
	- Happens when two or more constructors are created in a class. Essentailly provides us with two more methods of creating that class via its constructor
9. What is the use of super keyword in Java?
	- Super refers to the superclass (parent) objects. it is used to call superclass methods and access the superclass constructor
10. What is the difference between static methods, static variables, and static classes in Java?
	- Static keyword is mainly used for memory management. it essentially applies to the class it is implemented in, and acts like a constant
		- Variables: states that a single copy of that variable is created and shared among all objects at the class level. Can be viewed as global variables
		- Methods: essentially it is a method that is essentially considered constant as well. It is used to only interact with static variables and methods
		- Classes: can only be a static class if it is a nested class. Static classes cannot access non-static memebers of the outer class
11. What exactly is System.out.println in Java?
	- it essentially is used to print an argument that is passed into it.
		- System: final class defined in the java.lang package
		- Out: instance of the PrintStream type, which is a public and static member field of the System class
		- println(): all instances of the PrintStream class have a public method println() that we can invoke. It essentially prints out our argument, following that it will add a new line.
12. What part of memory - Stack or Heap - is cleaned in the garbage collection process?
	- Garbage collection collects the heap memory. usually the stack memory is collected automatically when the execution path reaches the end of the scope

## Object-Oriented Programming

- 1. What are the Object Oriented Features supported by Java?
	- One feature is abstraction. Essentially it is a method to break down complicated classes into manageable chunks. It also provides a method of hiding the implementation details of a class or method from the outside world.
	- Encapsulations, lets us resuse funcionality without jeopardizing our security. it essenitally binds data variables and methods together in a class. Only objects of the class, can access these entities. Also known as data hiding and helps with the insulation of data
	- Inheritance, process by which one class inherits the fucntions and properties of another class. Essentially subclasses can define their own features, and derive the res from the parent class.
	- Polymorphism, essentially allows for assigning and performing several actions using a signle fucntion
	- Objects and classes, the basic units of OOP.
- 2. What are the different access specifiers used in Java?
	- public, private, default, protected
- 3. What is the difference between composition and inheritance?
	- can be explained by what the relationship is
		- Inheritance "IS A" relationship while composition "HAS A" relation ship. typically composition is used when having another class be declared in the field variables of your current class. Inheritance lets us inherit certain methods and fields from a parent class, telling us that the current class is based off of the other class.
- 4. What is the purpose of an abstract class?
	- Abstract class can be used as a type of template for other classes. The class will hold common functionality for all classes that extend it. Essentially it is a class that cannot be instantiated, but it can be implemented by other classes that will use methods from it or overried them
- 5. What are the differences between constructor and method of a class in Java?
	- Constructor intiates the Object, based on a predetermined usage of fields. methods in the class essentially let us know what the class can or will do.
- 6. What is the diamond problem in Java and how is it solved?
	- it is essentially an issue where a child class inherits from multiple parent classes, and those parent classes have the saem methods with the same parameters. Java solves this issue using interfaces. These interfaces contain all the methods by default as abstract and we can provide the logic needed for that method, in the child class.
- 7. What is the difference between local and instance variables in Java?
	- Instance variables are declared within the class but outside of a method, constructor, or code block, and will always have a default value. They are created with the object and destroyed alongside it
	- Local variables are created within the method but do not get a default value. They are destroyed after the scope of the method is completed or exited out of.
- 8. What is a Marker interface in Java?
	- A marker interace is an interface that does not have any methods or constants inside of it. They are usually seen as issues since they do not define any behaviors within the interface

## Data Structures and Algorithms

- 1. Why are strings immutable in Java?
	- Strings are stored in a pool to save memory. The immutability ensures that one reference does not change the value for others pointing to the same string. Strings are used for sensitive data like usernames and passwords. The immutability prevents attackers from altering the values. Thread safety, since they cannot be changed they are auto thread safe, meaning multiple threads can use the same string. Performance wise, JVM resuses the strings in the string pool.
- 2. What is the difference between creating a String using new() and as a literal?
	- when using a string literal, we are doing something called string interning, meaning the references point to a similar location in memory. On the other hand, creating a new string object, essentially creates a unique string that is not referenced in the same memory.
- 3. What is the Collections framework?
	- it is a gourp of objects presented as a single unit. essentially any class or interface that implements some kind of collecting ds, it is apart of this fraemwork
- 4. What is the difference between ArrayList and LinkedList?
	- While both implement the list interface.
		- Arraylist is a dynamic array, that stores elements. Accessing an index it fast but insertion/deletion is rather slow
		- Linkedlist uses a doubly linked list to store elemetnts. elements contain a reference to the previous and next elements. Slower for random access, since the list must be traversed, but insertion and deletion is quick.
- 5. What is the difference between a HashMap and a TreeMap?
	- Both implement the map interface
		- Hashmap uses a hash table to store key-value pairs. It does not have a specific order of elements, but access time for elements is quiuck.
		- Tree map is a red-black tree BST to store key-value pairs. it maintains elements in a sorted order based on their keys, but its access time is much slower.
- 6. What is the difference between a HashSet and a TreeSet?
	- Both implement the set interface, which means they only store unique elements, no duplicates
		- Hashset is backed by the hashtable. Elements are not particularly ordered and offers constant time performance for basic operations like add, remove, and contains. Also allows null elements
		- TreeSet is backed the a red and black tree (BST). Elements are stored in teheir natural sorted order. Performance wise it offers logarithmic time performance for basic operations. But it does not allow null values
- 7. What is the difference between an Iterator and a ListIterator?
	- Both are interfaces used to traverse colelctions.
		- Iterator traverses in the forward direction only.
		- Listiterator can traverse back or forward.
- 9. What is the purpose of the Comparable interface?
	- it is used to define the natural ordering of objects for a user-defined calss.
- 11. What is the purpose of the java.util.concurrent package?
	- It covers concurrency, multithreading, and parallelism on the java platform.

# Exception Handling

- 1. What is an exception?
- 2. How does an exception propagate throughout the Java code?
- 3. What is the difference between checked and unchecked exceptions?
- 4. What is the use of try-catch block in Java?
- 5. What is the difference between throw and throws?
- 6. What is the use of the finally block?
- 7. What’s the base class of all exception classes?
- 8. What is Java Enterprise Edition (Java EE)?
- 9. What is the difference between a Servlet and a JSP?
- 10.What is the purpose of the Java Persistence API (JPA)?
- 11.What is the difference between stateful and stateless session beans?

# Multithreading

- 1. What is a thread and what are the different stages in its lifecycle?
- 2. What is the difference between process and thread?
- 3. What are the different types of thread priorities available in Java?
- 4. What is context switching in Java?
- 5. What is the difference between user threads and Daemon threads?
- 6. What is synchronization?
- 7. What is a deadlock?
- 8. What is the use of the wait() and notify() methods?
- 9. What is the difference between a thread and a process in Java?
- 10. What is the difference between synchronized and volatile in Java?
- 11.What is the purpose of the sleep() method in Java?
- 12.What is the difference between wait() and sleep() in Java?
- 13.What is the difference between notify() and notifyAll() in Java?

a bean is the backbone of waht spring is. Essentially it contains a recipe for creating an actual instance of a class. It helps with dependency injectsion. Maintined by the IoC container
IoC container is the dependency injection, it essentially jelps objects define their dependencies, that is, the other objects they work with.  The container then injects the dependencies whenever the bean is created.