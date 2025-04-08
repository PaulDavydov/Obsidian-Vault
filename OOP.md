### Encapsulation

```
public class Item {
	private String name;
	private int quantity;
}

public class Inventroy {
	private ArrayList<Item> list;
	public Inventory() {
		list = new ArrayList();
	}
	public void addItem(Item item) {
		items.add(item);
	}
}
```
- private, a modifier that says a method/variable cannot be accessed outside of a class
	- one way to access/modify these private variables, is to use getter/setter methods
- Encapsulation, keeps details of a object inside of a class, and only accessible via select methods
- ArrayList is a data structure that stores objects in a ordered fashion


### Inheritance
```
public class Fruit extends Item {
	private String type;

	public Fruit(String type, String name, int quantity) {
		super(name, quantity); // instead of using this. this., use super and vars that you are passing in from parent
		this.type = type;
	}
}

public class Weapon extends Item {
	private int damage;
	prvate String type;

	public Weapon(String name, int quantity, int damage, String type) {
		super(name, quantity);
		this.damage = damage;
		this.type = type;
	}
}

public void main() {
	Item item = new Item("Generic item", 10) // name, quantity
	Fruit fruit = new Fruit("Fuji", "Apple", 20) // type, name, quantity
}
```
- item is the superclass and Fruit is the child to Item.
	- Fruit will inherit the attributes from Item, such as name, quantity


### Polymorphism
- overriding and overloading are examples of polymorphism
	- When a parent class has a method you want to use but add additional logic, you can use the @Override annotation to call the method and apply your own logic 
	- This is known as runtime polymorphism, where a method acts differently based on how you code it
	- everything is resolved at runtime
```
public item {
	public String toString() {
		return string;
	}
}

public fruit extends item {
	@Override
	public String toString() {
		return "Hi" + string + fruit.getName
	}
}
```

- overloading, multiple methods with the same name, but take in different parameters
```
public class item {
	public void create(String type) {}
	public void create(String type, String name)
}
```
- both methods may be the same name, but they are called based on the parameters passed into them
	- this is compile time polymorphism
	- erorrs are easier to find and spot since these only happen during compile time

### Abstraction
- hiding complex implementation features of a objects, and only showing the essentials features of the object
- Can use the abstract keyword
- 
```
	public class abstract item{
		public abstract void displayinfo();
	}
```
- essentially means we can no longer create objects using the Item class, instead we can use it to make subclasses of Item
- For methods, when a abstract parent class implements a abstract method, it does not need to provide the logic for it. Instead a handshake is made where the children of that parent, will provide the logic for that method
	- Override it using polymorphism
- another way to abstract things in java, is to create a interface
	- it can contain all common items, and methods that inherited subclasses must override and apply their own logic
- What makes interfaces good, is that it allows the subclass to implement from multiple interfaces. Meaning logic from differents parents of your code can be implemented.
- on the other hand, when inheriting from a class, a child class can only extend to one parent class