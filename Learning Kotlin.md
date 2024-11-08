- Similarly to how a JAVA program is written, the main() function is where the program begins, and will have curly braces around it
- Kotlin stores data in variables
	- Read-only variables: `val`
	- mutable variables: `var`
- To assign a value to a variable, use the assignment operator `=`
- to print contents of variables to standard output, you can use the string templates
	- converts the data to strings
	- Use `"`
	- Template expressions always start with dollar sign `$`
	- To evaluate a piece of code in a template expressions, place the code within curly braces `{}` after the dollar sign `$` 
EXAMPLE

```kotlin
fun main() {
	val name = "Mary"
	val age = 20
	println("$name is $age years old)
}
```

- Every variable and data structure in Kotlin has a data type
	- IMPORTANT due to it telling the compiler what you are allowed to do with that variable or data structure
	- basically tells the compiler what functions and properties
- Kotlins ability to infer the data type is called type inference
	- if assigned a `int` value, compiler knows that it can perform arithmetic operations with that value

| Category               | Basic types                  |
| ---------------------- | ---------------------------- |
| Integers               | `Byte, Short, Int, Long`     |
| Unsigned Integers      | `UByte, UShort, UInt, ULong` |
| Floating-point numbers | `Float, Double`              |
| Booleans               | `Boolean`                    |
| Characters             | `Char`                       |
| Strings                | `String`                     |

- You can declare variables in Kotlin, and initialize them later
	- Must be initialized before the first read
- To declare a variable without initializing it, specify it with `:` > [!note]- 
Typical way of declaring a <abbr title="val NAME : DATA_TYPE = INITIAL_VAL">variable</abbr>

val ==name==: ==data type== = ==initial value==


```kotlin
// Declare var without initialization
var d: Int
// Variable initialized
d = 3

// Variable explicitly typed and initialized
val e: String = "hello"
```


Example of using data types

```kotlin
fun main() {
	val a: Int
	val b: String

	a = 1000
	b =  "log message"
	val c: Double = 3.14
	val d: Long = 100_000_000_000
	val e: Boolean = false
	val f: Char = '\n'
}
```

- Kotlin has ways to group data into structures for later processing


| <abbr title="each collection type can be mutable or read only">Collection Type</abbr> | Description                                                             | Documentation                                                                                                                                                                    |
| ------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Lists                                                                                 | Ordere collections of items                                             | [List](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/) [MutableList](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list.html) |
| Sets                                                                                  | Unique unordered collections of items                                   | [Set](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-set/) [MutableSet](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-set/)         |
| Maps                                                                                  | Sets of key-value pairs where keys are unique and map to only one value | [Map](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/) [MutableMap](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/)         |

# List
- Lists store items in the order that they are added, and allow for duplicate items
	- To create a read-only `List` use the `listOf()` function
		`val list = listOf('a', 'b', 'c')
	- To create mutable list `MutableList` use the `mutableListOf()` function
		`var list = mutableListOf<Int>()`
		`var list = mutableListOf(1,2,3)`
	- Casting is when you take the values of a mutable list, and assign to a read-only List to prevent unwanted modifications

| Commands         | Description                                       |
| ---------------- | ------------------------------------------------- |
| `list[x]`        | access an item in a list, indexed access operator |
| `list.first()`   | first item in a list                              |
| `list.last()`    | last item in a list                               |
| `list.count()`   | number of items in a list                         |
| `in`             | check if item is in a list: `"circle" in list`    |
| `list.add(x)`    | add items into a list                             |
| `list.remove(x)` | remove items from a list                          |

# Set
- Sets are unordered and only store unique items
	- To create a read-only `Set` use the `setOf()` function
		`val set1 = setOf(1,2,3)` 
	- To create a mutable `Set` use the `mutableSetOf()` function
		`val set = mutableSetOf<Int>()`
		`set.add(1)`
		`val set = mutableSetOf(1,2,3)`
	- When creating sets, Kotlin can infer the type of items stored
	- Similary to lists, to prevent unwanted modifications, take the mutable sets and cast them to a read-only Set
	 `val fruit: MutableSet<String> = mutableSetOf("apple","banana")`
	 `val fruitLocked: Set<String> = fruit`
	 - Since sets are unordered, you can not access an item at a particular index

| Commands        | Description                                  |
| --------------- | -------------------------------------------- |
| `set.count()`   | number of items in a set                     |
| `in`            | check if item is in a set: `"banana" in set` |
| `set.add(x)`    | add item into mutable set                    |
| `set.remove(x)` | remove item from a set                       |

# Map
- Maps store items as a key-value pairs
`KEY : VALUE `
- Access the values by referencing the key
- Each key in a map must be unique but you can have duplicate values
- To create a read-only `Map` use the `mapOf()` function
	`val map = mapOf(1 to "x", 2 to "y")`
- To create a mutable `Map` use the `mutableMapOf()` function
	`val map = mutableMapOf(1 to "x", 2 to "y")`
	`val map = mutableMapOf<Int, Any?>()`
	`map[1] = "x"`
- Once again, when creating a map, Kotlin can determine the type of items being stored
- Same as previous collections, to prevent unwanted modifications, take a mutable Map and cast it into a read-only Map

| Commands                | Description                                                                   |
| ----------------------- | ----------------------------------------------------------------------------- |
| `to`                    | Adds key and value pair: `"apple" to 100`                                     |
| `map[x]`                | Access a value in a map                                                       |
| `map.count()`           | get the number of items in a map                                              |
| `map.put(key, value)`   | add items into a mutable map                                                  |
| `map.remove(key)`       | remove items from a mutable map                                               |
| `maps.containsKey(key)` | check if specific key is already included in a map                            |
| `map.keys`              | obtain a collection of the keys in a map                                      |
| `maps.values`           | obtain a collection of the values in a map                                    |
| `in`                    | Check if key or value is in a map: `"orange" in map.keys` `200 in map.values` |

## if
- Kotlin does provide conditional expressions
	```kotlin
	val d : Int
	val check = true

	if(check) {
		d = 1
	} else{
		d = 2
	}
	println(d)
	// 1
```

- No ternary operator in Kotlin, instead `if` can be used as an expression
- When using `if` as an expression, there are no curly braces `{}`
	```kotlin
	val a = 1
	val b = 2
	println(if (a > b) a else b)
```

## When
- `When` is used when a conditional expression has multiple branches
	- Can be a statement or expression
- Place conditional expression within `()` and the actions to take within a `{}`
- use `->` in each branch to separate each condition from each action
Using `when` as a statement
```kotlin
val obj = "Hello"

when (obj) {
	// Check if obj is equal to 1
	"1" -> println("One")
	// Checks whether obj equals to "Hello"
	"Hello" -> println("Greeting")
	// Default statement
	else -> println("Unknown")
}
// Greeting would be the output
```

Using `when` as a expression
```kotlin
val obj = "hello"

val result = when(obj) {
	//if obj equals "1", sets result to "one"
	"1" -> "One"
	//if obj equals "Hello", set result to "Greeting"
	"Hello" -> "Greeting"
	//sets result to "Unknown" if no prev condition is satisfied
	else -> "Unknown"
}
println(result)
// Greeting
```
- When using as a expression, `else` branch is mandatory, unless the compiler can detect that all possible cases are covered by the branch conditions
Example of `when` to check a chain of Boolean expressions:
```kotlin
val temp = 18

val desc = when {
	temp < 0 -> "very cold"
	temp < 10 -> "a bit cold"
	temp < 20 -> "warm"
	else -> "hot"
}
println(desc)
// warm
```

## Ranges
- Ranges are indicated in a loop by `..`
	- `1..4` = `1,2,3,4`
- To not include the end value, use `..<` 
	- `1..<4` = `1,2,3`
- To reverse order a range, use `downTo`
	- `4 downTo 1` = `4,3,2,1`
- To go more than 1, use `step`
	- `1..5 step 2` = `1,3,5`
- Can do the same for `Char` ranges
	- `'a'..'d'` = `'a','b','c','d'`
	- `'z' downTo 's' step 2` = `'z','x','v','t'`

## Loops
- `for` loop 
- `while` loop
	- can be used to execute a code block while a conditional expression is true (`while`)
	- execute a code block first and then check the conditional expression (`do-while`)

## Functions
- declare functions using the `fun` keyword
- Function parameters are written within parentheses `()`
	- each parameter must have a type and must be separated by `,`
- Return type of the function is written after the `()` and separated by a colon `:`
- Body of function is written in the `{}`
```kotlin
fun sum(x: Int, y: Int): Int {
	return x+y
}

fun main() {
	println(sum(1,2))
	// 3
}
```

- When writing parameters for a function, you can include the name of parameter when calling the fucntion
	- Does not have to be in order if named arguments used
```kotlin
fun print(message: String, prefix: String) {
	println("[$prefix] $message")
}

fun main() {
	print(prefix = "Log", message = "hello")
	//[Log] hello
}
```

- can set default values for function parameters
	- any parameter with a default value can be omitted when calling your function

## Lambda expressions
- lambda exoressions are written within `{}` 
	- You then write the parameters followed by the `->`
	- The functioin body follows that
- To assign a lambda expression to a variable, use `=`
`val string = {string: String -> string.uppercase()}`
- Passing a lambda expression to a function, use the `.filter()`on collections
- `.map` is used to transform items in a collection

## Classes
- To declare a class, use the `class` keyword
- Characteristic of a class object can be declared in properties.
	- use `()` after the class name
	- Body of class defined by `{}`
	- Recommended to declare properties as read only
- To create a object from. a class, you declare a class instance using a constructor
	```kotlin
	class Contact(val id: Int, var email: String)

	fun main() {
		val contact = Contact(1, "string")
	}
```

- To access a property of a class, use `.` after the object
- Kotlin has data classes which store data
	- Have same functionality as classes
	- Have preexisting member functions

DATA CLASSES USEFUL MEMBER FUNCTIONS

| Function            | Description                                                                                     |
| ------------------- | ----------------------------------------------------------------------------------------------- |
| `.toString()`       | Prints a readable string of the class instance and its properties, `println` works the same way |
| `.equals()` or `==` | Compares instances of a class                                                                   |
| `.copy()`           | Creates a class instance by copying another, potentially with some dif properties               |
## Null Safety
- Kotlin can have null values and has safeties in place that detects potentioal probs will null values at compile time
	- explicitly declare when `null` values are allowed in your program
	- check for `null` values
	- use safe calls to properties or functions that may contain `null` values
	- declare actions to take if `null` values are detected
- Kotlin also supports nullable types, which allows the possiblity for the declared type to have `null` values
- Nullable types are declared by explicitly adding `?` after the type declaration
	`var nullable: String? = "This can be null"`
- Check for presence of `null` values using conditional expressions
- `describeString()` function has an `if` statement that checks whether a string is not `null` and its `length` is greater than zero
```Kotlin
if(x != null && x.length > 0) {
	return "String of length ${x.length}"
} else {
	return "x is a null string"
}
```

- to safely acces properties of an object that might contain a `null` value, use the safe operator `?`
	- Safe call operator returns `null` if either the object or one of its accessed properties is `null` 
		- Good way to avoid errors that can be caused by `null` values
- you can provide a default value for null values using the Elvis operator `?:`
	```Kotlin
	println(nullString?.length ?: 0)
	// prints 0
```



Scanners are used to obtain data standard input
- Need to import that value:
	- `import java.util.Scanner`
	- Create a variable initialized by Scanner:
		`val scanner = Scanner(System.in)`
	- `scanner.close() to stop the scanner`
	- Can directly input data into Scanner
		`val scanner = Scanner("123_456)`
	- use the delimiter to split the two number
		`scanner.useDelimiter("_")`
		