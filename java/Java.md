


# Java
> Java = Compiled + Interpreted. 
First the Code is compiled into bytecode resulting in .class file. After this the code is interpreted/executed at runtime


## JAVA Versions

## Java 17:  
### ‘record’ Type
<span style="background-color: #EA738D">record classes are a special kind of immutable class which is meant to replace data transfer objects(DTOs).</span> Normally if we want to use some POJO inside our class or methods, we would have to declare the class along with defining all the getters, setters, equals and hashcode functions. For example to use a sample Fruit class in other places, we would have to define our class someway like below:


	
```java
public class Fruit {
	private String name;
	private int price;

    //getters, setters, equals and hashcode methods
}
```
Although we can reduce most of our boilerplate code by using libraries like lombok, we can still reduce it even further with the help of records. <span style="background-color: #EA738D">With records the same code becomes:</span>

```java
public static void doSomething() {
	record Fruit(String name, int price) {}
	Fruit fruit = new Fruit("Apple", 100);
	System.out.println(fruit.getPrice());
}
```	
As we can see, we can even define method local record objects. <span style="background-color: #EA738D">The records object automatically provides us with getter, setter, equals and hashcode methods for all its fields.</span>

<span style="background-color: #EA738D">The fields inside the record cannot be changed,</span> and it can only be defined by the arguments given when declaring the record as shown above(but we can define static variables). We can also define a custom constructor which can validate the fields. <span style="background-color: #EA738D">It is recommended that we do not override the getters and setters of records which could affects its immutability.</span> An example of a record with multiple constructors and static variables and methods is shown below:

```java
public record Employee(int id, String firstName,
	        String lastName) {
	
	    static int empToken;
	
	    // Compact Constructor
	    public Employee {
	        if (id < 100) {
	            throw new IllegalArgumentException(
	                    "Employee Id cannot be below 100.");
	        }
	        if (firstName.length() < 2) {
	            throw new IllegalArgumentException(
	                    "First name must be 2 characters or more.");
	        }
	    }
	
	    // Alternative Constructor
	    public Employee(int id, String firstName) {
	        this(id, firstName, null);
	    }
	
	    // Instance methods
	    public void getFullName() {
	        if (lastName == null)
	            System.out.println(firstName());
	
	        else
	            System.out.println(firstName() + " "
	                    + lastName());
	    }
	
	    // Static methods
	    public static int generateEmployeeToken() {
	        return ++empToken;
	    }
	}

```
	
Some more qualities of record classes are:
1. You can use nested classes and interfaces inside a record.

2. You can have nested records too, which will implicitly be static.

3. A record can implement interfaces.

4. You can create a generic record class.

5. Records are serializable.


### ‘sealed’ Classes
sealed class will give us more control over which classes are allowed to extend our classes. <span style="background-color: #EA738D">In Java 11, a class can be final or extended. If you want to control which classes can extend your super class, you can put all classes in the same package and you give the super class package visibility. However, it is not possible anymore to access the super class from outside the package.</span> As an example, see the code below:

```java
public abstract class Fruit {}

public final class Apple extends Fruit {}

public final class Pear extends Fruit {}

private static void problemSpace() {
    Apple apple = new Apple();
    Pear pear = new Pear();
    Fruit fruit = apple;
    class Avocado extends Fruit {};
}
```
 
Here, <span style="background-color: #EA738D">we cannot stop Avocado to extend the Fruit class.</span> If we make the Fruit class default, then the assignment of apple to fruit object would not compile. Hence, now we can use sealed classes to allow only specific classes to extend our superclass. An example is given below:

```java
public abstract sealed class Vehicle permits Car, Truck {

    protected final String registrationNumber;

    public Vehicle(String registrationNumber) {
        this.registrationNumber = registrationNumber;
    }

    public String getRegistrationNumber() {
        return registrationNumber;
    }

}
```

A permitted subclass must define a modifier. It may be declared final to prevent any further extensions:
<span style="background-color: #EA738D">Either mark them as Final to prevent further extension or Mark them as `non-sealed` for further extension.</span>



```java
public final class Truck extends Vehicle {}

    OR,

public non-sealed class Car extends Vehicle{}
```
  	
  
 
<span style="background-color: #EA738D">As we see, we use a new keyword sealed to denote that this is a sealed class. We define the classes that can be extended using the permits keyword. Any class which extends the sealed class can be either final like Truck or can be extended by other classes by using the non-sealed keyword when declaring the class as with Car.</span>


## JDK 21 Features:

### 1. Generational ZGC (Z Garbage Collector)
ZGC does provide lower pause times than G1GC for low latency applications.
```bash
java -XX:+UseZGC -XX:+UseGenerationalZGC -jar yourapp.jar
```

###2. VirtualThread:
Use virtual threads only for code that involves blocking, such logging, file I/O, accessing databases, network calls. 


---


### The **compiler is not backwards compatible** because Java5 code JDK won't run in Java 1.4 jvm (unless compiled with the -target 1.4 flag). 
### But **the JVM is backwards compatible**, as it can run older code JRE5 can run 1.4 code.

In brief, we can say:

> JDK's are (usually) forward compatible.
JRE's are (usually) backward compatible.
> 

---


`JVM is called virtual because it provides an interface that does not depend on the underlying operating system and machine hardware. This independence from hardware and the operating system is what makes java program write-once-run-anywhere.`

![https://beginnersbook.com/wp-content/uploads/2013/05/jdk.jpg](https://beginnersbook.com/wp-content/uploads/2013/05/jdk.jpg)

---

# Garbage Collection:

### GC Pause:
> The garbage collector needs to identify unused memory and reclaim it. During this process, the program may be stopped to ensure data consistency while the memory is being examined and managed.

**Problem:**
During a GC pause, the application is not running your code, it is essentially frozen, stopped. Therefore, if this happens in the middle of a website request and the pause lasts 200ms, that's 200ms for the client to wait for their page to load. 

JDK 8: The default collector in JDK 8 is Parallel GC. which has higher GC pause 200ms.
JDK17: G1GC has lower GC pause, also new GC ZGC has much lower pause.

1. G1GC works by dividing the old generation in multiple regions.
2. G1GC concurrently identifies unused objects (vacant houses) in the background while the city (application) keeps running.
3. Once a district (region) is marked, G1GC pauses the city (application) briefly. Live objects (occupied houses) are then carefully moved to another district (region), freeing up memory in the evacuated district for future use. 
4. G1GC focuses on regions with the most unused objects (garbage). This ensures that the evacuation pauses are minimized.


1. **Young** Generation
    
    **New objects are allocated in Young generation.**
    
    **Young** Generation consists of >
    
    - 1a) **Eden**,
    - 1b) **S0 (Survivor** space 0**)**
    - 1c) **S1 (Survivor** space 1**)**
    
    Minor garbage collection occurs in Young Generation. Some of the objects which aren't cleaned up survive in young generation and gets aged. Eventually such objects are moved from young to old generation.
    
2. The **Old Generation** is used for storing the long surviving aged objects (Some of the objects which aren't cleaned up **survive in young generation and gets aged**.  Eventually such objects are **moved from young to old generation**).
    
    ***Major garbage collection** occurs in* **Old Generation.**
    
    All the non-daemon threads running in application are stopped during major garbage collections.
    
    [Daemon threads](http://www.javamadesoeasy.com/2015/03/daemon-threads-12-salient-features-of.html) performs major garbage collection.
    

3. Permanent generation Space contains metadata required by JVM to describe the classes and methods used in the application. The permanent generation space is populated at runtime by JVM based on classes in use in the application.

The permanent generation is included in a full garbage collection in java.

PermGen is the memory area in Heap that is used by the JVM to store class and method objects
if there are many classes that are getting added in permGen,
it would throw

```java
java.lang.OutOfMemoryError: PermGen space
```

Metaspace is NOT part of Heap. Rather Metaspace is part of Native Memory (process memory) which is only limited by the Host Operating System.

![http://karunsubramanian.com/wp-content/uploads/2014/07/Java8-heap.jpg](http://karunsubramanian.com/wp-content/uploads/2014/07/Java8-heap.jpg)

While you will NOT run out of PermGen space anymore (since there is NO PermGen), you may consume excessive Native memory making the total process size large. The issue is, if your application loads lots of classes (and/or interned strings), you may actually bring down the Entire Server.
`So its important to put flag for metaspace size in config`

Heap is divided into sections which allows easy allocation and cleanup. Eden Space is the place where new objects are allocated. When Eden is about to reach its limit, there is a Minor GC which clears up objects available for GC from Eden space to one of the Survivor spaces (S0 and S1). Also the objects available for GC in one of the survivor spaces are cleared and remaining objects move to the other survivor space. So at a time there is always one survivor space which is empty. After n (depends on JVM implementation) such iteration objects which have survived are moved to the old memory. Whenever old memory is on the verge of being a full Major GC occurs and unused resources of old memory are cleared.

- For any given object, `finalize()` will be called only once (at most) by the garbage collector. Also, the garbage collector is not guaranteed to run at any specific time.
- The `java.lang.Object.finalize()` is called by the garbage collector on an object when garbage collection determines that there are no more references to the object. A subclass overrides the finalize method to dispose of system resources or to perform other cleanup.
    
    > `finalize()` is deprecated.
    > 
    
    ```java
    Car car= new Car();
    car.finalize();
    ```
    
- if overridding `finalize()` it is good programming practice to use a try-catch-finally statement and to always call super.finalize(). This is a safety measure to ensure you do not inadvertently miss closing a resource used by the objects calling class
    
    ```java
    protected void finalize() throws Throwable {
        try {
            close();        // close open files
        } finally {
            super.finalize();
        }
    }
    ```
    
## Heap vs Stack

<span style="background-color: #EA738D">Each Java virtual machine thread has a private stack, created at the same time as the thread.</span>
**local variables and methods are on the Java stack**.
**All Objects are created in HEAP and the reference is in the stack.**
It Causes StackOverflow Error.

**String pool is created in now ( java 8 ) heap memory instead of permgen. to store string literals: `String s = "abc"`**

**STRING POOL Doesn't have String Literals**

String pools are hash table containing the reference to the String object residing in heap in this case `"abc"` 

The Java virtual machine has a heap that is shared among all Java virtual machine threads. The heap is the runtime data area from which **memory for all class instances and arrays is allocated**
The memory leaks will be in the heap. It Causes OutOfMemoryError. 

---

## Object Orientation Design

### SOLID:

 1. **Single Responsibility Principle**: 
 A class should have one reason to change. 
	 ```java
	 public class Invoice{
	     public void AddInvoice(){ 
	         ...
	     }
	     public void DeleteInvoice(){ 
	         ...
	     }
	     public void GenerateReport(){ 
	         //this function should be moved to different class
	    }
	    public void EmailReport() { 
	         //this function should be moved to different class
	    }
	}
	``` 
 3. **Open Closed Principle**: 
 _Entities should be open for extension, but closed for modification._
 you can create classes and extend them (by creating a subclass), but you shouldnt  modify the original class. Like adding a new field is fine, but not modifying existing fields. 
 
 4. **Liskov Substitution Principle:** 
 A class that expects an object of type animal should work if a subclass dog and a subclass cat are passed. Also Animal should not have a bark function. As cats cant bark.
			 **Bad example**--

	```java
	public class Bird{
	    public void fly(){}
	}
	public class Duck extends Bird{}
     ```
     The duck can fly because it is a bird, but what about this:
	```java
	public class Ostrich extends Bird{}
	```
	Ostrich is a bird, but it can't fly, Ostrich class is a subtype of class Bird, but it shouldn't be able to use the fly method, that means we are breaking the LSP principle.
**Good example**

	```java
	public class Bird{}
	public class FlyingBirds extends Bird{
	    public void fly(){}
	}
	public class Duck extends FlyingBirds{}
	public class Ostrich extends Bird{} 
	```
 5. **Interface Separation**: 
 An **interface** shouldn't **force** a **class** to implement methods that it won't be using.
 Keep your interfaces the smallest you can. A teacher that also is a student should implement both the IStudent and ITeacher interfaces, instead of a single big interface called IStudentAndTeacher.
 
 6. **Dependency Inversion (NOT dependency Injection)** :  
Imagine you have a car and your different components are:
	```java
		1.  Client: You as the person driving the car.
		2.  High-Level Modules: The steering wheel and the gas/brake peddles.
		3.  Low-Level Modules: Engine
	``` 
	**Abstractions don't depend on details.**
	For me, it doesn't matter whether my engine has changed or not, I still should be able to drive my car the same way.
	**Details should depend upon abstractions.**
	I would not want an engine that causes the brake to double the speed. Meaning a engine should do what it says drive the car.
	```java
		interface DatabaseInterface {
		    public function get();
		    public function insert();
		    public function update();
		    public function delete();
		}
		class MySQLDatabase implements DatabaseInterface {
		    //all implemented functions get(), insert() ....
		    }
		}

		class MongoDB implements DatabaseInterface {
		     //all implemented functions get(), insert() ....
		}

		class BudgetReport {
		    private DatabaseInterface database;

		    public BudgetReport(DatabaseInterface database)
		    {
		        this.database = database;
		    }

		    public function open(){
		        this.database.get();
		    }

		    public function save(){
		        this.database.insert();
		    }
		    ...
		}

		// Main Function: Client
		DatabaseInterface mysql = new MySQLDatabase();
		report_mysql = new BudgetReport(mysql);

		report_mysql.open();

		DatabaseInterface mongo = new MongoDB();
		report_mongo = new BudgetReport(mongo);

		report_mongo.open();
	```
	Now here BudgetReport doesnt care about the underlying implementation whether its mySQL or MongoDB.  This also works with Open-Close Principle, as we can add a new database class that implements the `DatabaseInterface` and we dont need to change BudgetReport class.

	Another Example: 

	```java
	interface Logger {
	   public void write(String message);
	}
	 
	class FileLogger implements Logger {
	   public void write(String message) {
	       // write to file
	   }
	}
	 
	class StandardOutLogger implements Logger {
	   public void write(String message) {
	       // write to standard out
	   }
	}
	 
	public void doStuff(Logger logger) {
	   // do stuff
	   logger.write("some message")
	}

	```
	If you’re writing code that needs a logger, you don’t want to limit yourself to writing to files, because you don’t care. You just call the  `write`  method and let the concrete class sort it out.

### Abstraction

abstraction is achieved by interfaces and abstract classes . Interfaces allows you to abstract the implemetation completely while abstract classes allow partial abstraction as well.

### Encapsulation

Make members of a class as private.
Define public setter and getter methods to modify and view the variables' values and access them outside the class only through getters and setters

### Polymorphism

1.Compile-time polymorphism/static binding/early binding/overloading. 

2.Run-time polymorphism /dynamic binding/late binding/overriding.

### Inheritance

Inheritance is declared using the extends keyword.
Inheritance is an "is-a" relationship. Composition is a "has-a".

### Association

The association relationship indicates that a class knows about, and holds a reference to, another class. Associations can be described as a "has-a" relationship because the typical implementation in Java is through the use of an instance field.
Association in Java:

1. Two separate classes are associated through their objects.
2. The two classes are unrelated, each can exist without the other one.
3. Can be a one-to-one, one-to-many, many-to-one, or many-to-many relationship.

Aggregation is a narrower kind of association. It occurs when there’s a one-way (HAS-A) relationship between the two classes you associate through their objects. **For example, every Passenger has a Car but a Car doesn’t necessarily have a Passenger.**

Aggregation in Java:

1. One-directional association.
2. Represents a HAS-A relationship between two classes.
3. Only one class is dependent on the other.

Composition
Compositionis a stricter form of aggregation. It occurs when the two classes you associate are mutually dependent on each other and can’t exist without each other. For Example, Car has an Engine. here either Car or Engine cannot
exist without each other.
Composition in Java:

1. A restricted form of aggregation
2. Represents a PART-OF relationship between two classes
3. Both classes are dependent on each other
4. If one class ceases to exist, the other can’t survive alone.

### Java is always pass-by-value. Unfortunately, when we pass the value of an object, we are passing the reference to it.

---

- Null is not a valid object instance, so there is no memory allocated for it. It is simply a value that indicates that the object reference is not currently referring to an object.
- Each Java application uses an independent JVM.
Each JVM is a separate process, and that means there is no sharing of stacks, heaps, etcetera. (Generally, the only things that might be shared are the read only segments that hold the code of the core JVM and native libraries ... in the same way that normal processes might share code segments.
Garbage collection operates on each JVM independently.

---

## Reflection

Java Reflection is the process of analyzing and modifying all the capabilities of a class at runtime. Reflection API in Java is used to manipulate class and its members which include fields, methods, constructor, etc. at runtime.

Say you have an object of an unknown type in Java, and you would like to call a 'doSomething' method on it if one exists. Java's static typing system isn't really designed to support this unless the object conforms to a known interface, but using reflection, your code can look at the object and find out if it has a method called 'doSomething' and then call it if you want to.
So, to give you a code example of this in Java (imagine the object in question is foo) :

```java
Method method = foo.getClass().getMethod("doSomething", null);
method.invoke(foo, null);   //The null indicates there are
```

no parameters being passed to the foo method

*One very common use case in Java is the usage with annotations. JUnit 4, for example, will use reflection to look through your classes for methods tagged with the @Test annotation, and will then call them when running the unit test.*

```java
Class.forName(property.getProperty("DB_DRIVER_CLASS"));
```

---

## Cloneable Interface

- It is used to indicate that a class allows a bitwise copy of an object (that is, a clone) to be made. If you try to call clone( ) on a class that does not implement Cloneable, a CloneNotSupportedException is thrown. When a clone is made, the constructor for the object being cloned is not called.
- clone repeatedly up the chain until you have cloned an object, you have a shallow copy of the object. The clone generally shares state with the object being cloned. If that state is mutable, *you don't have two independent objects. If you modify one, the other changes as well*. And all of a sudden, you get random behavior.

### Marker interfaces

Marker Interfaces in java are interfaces with no members declared in them. They are just an empty interfaces used to mark or identify a special operation. For example, Cloneable interface is used to mark cloning operation and Serializable interface is used to mark serialization and deserialization of an object. **Now annotations are preferred as they don't propagate to sub-classes.**

### Inner interface

Inner interface is also called nested interface, which means declare an interface inside of another interface. Map.Entry is an nested Interface.

```java
public interface Map {
    interface Entry{
        int getKey();
    }
    void clear();
}
```

## CLASS

- Default access: a class with default access can be seen only by classes within the same package
- Public access: all classes in the Java Universe (JU) have access to a public class. The class can now be instantiated from all other classes, and any class is now free to subclass (extend from) it—unless, that is, the class is also marked with the nonaccess modifier final.
- Marking a class as `strictfp` means that any method code in the class will conform to the IEEE 754 standard rules for floating points. Without that modifier,floating points used in the methods might behave in a platform-dependent way.
- Final Classes When used in a class declaration, the final keyword means the class can't be subclassed. In other words, no other class can ever extend (inherit from) a final class, and any attempts to do so will give you a compiler error.`Can't subclass final classes.`
- Abstract Classes An abstract class can never be instantiated. Its sole purpose, mission in life, raison d'être, is to be extended (subclassed).
i.e. can’t do:`Car car = new Car();`

`AnotherClass.java:7: class Car is an abstract class.It can't be instantiated.`

You can, however,put nonabstract methods in an abstract class.

- A superclass can’t be instantiated because there can’t be any constructor in it.
- But we can instatntiate it using it as inner class in subclass.

	```java
	abstract class My {
	    public void myMethod() {
	        System.out.print("Abstract");
	    }
	}
	class Poly extends My {
	    public static void main(String a[]) {
	        My m = new My() {};
	        m.myMethod();
	    }
	}
	```

- *You can't mark a class as both abstract and final. They have nearly opposite meanings. An abstract class must be subclassed, whereas a final class must not be subclassed. If you see this combination of abstract and final modifiers, used for a class or method declaration, the code will not compile.*
    
    *modifier static not allowed here*
    
    ```java
    public class HelloWorld{
        static class Hello{}
        class InnerClass {}
    // arguments are passed using the text field below this editor
        public static void main(String[] args)
        {
            HelloWorld helloWorld = new HelloWorld();
            HelloWorld.Hello h= new  HelloWorld.Hello();
            HelloWorld.InnerClass i = helloWorld.new Hello();
        }
    }
    
    ```
    

---

### STATIC KEYWORD:

In Java, static keyword is mainly used for memory management. It can be used with variables, methods, blocks and nested classes. It is a keyword which is used to share the same variable or method of a given class. `Basically, static is used for a constant variable or a method that is same for every instance of a class.`

### Static Block

To initialize your **static variables**, you can declare a static block that gets executed exactly once, when the class is first loaded.

```java
public class BlockExample{
		// static variable
		static int j = 10;
		static int n;
		 
		// static block
		static {
		System.out.println("Static block initialized.");
		n = j * 8;
		}
}
```

### Static variables

Are, essentially, global variables. Basically, all the instances of the class share the same static variable.

### Static Method:

Methods declared as static can have the following restrictions:

- They can directly call other static methods only.
- They can access static data directly.

> Static Methods Can't access non-static ( instance) variables. as they are not related to instance of a class. but instance functions can call static variable.
> 

### Static Class:

Java has no way of making a top-level class static but you can simulate a static class like this:

- Declare your class `final` - Prevents extension of the class since extending a static class makes no sense
- Make the constructor `private` - Prevents instantiation by client code as it makes no sense to instantiate a static class
- Make **all** the members and functions of the class `static` - Since the class cannot be instantiated no instance methods can be called or instance fields accessed
- Note that the compiler will not prevent you from declaring an instance (non-static) member. The issue will only show up if you attempt to call the instance member.

> What good are static classes? A **good use of a static class** is in defining one-off, **utility and/or library classes** where instantiation would not make sense. A great **example is the Math class** that contains some mathematical constants such as PI and E and simply provides mathematical calculations. Requiring instantiation in such a case would be unnecessary and confusing. Notice that it is final and all of its members are static. If Java allowed top-level classes to be declared static then the Math class would indeed be static.

## Interface

- All interface methods are implicitly public and abstract. In other words, you do not need to actually type the public or abstract modifiers in the method declaration, but the method is still always public and abstract.
- Because interface methods are abstract, they cannot be marked final.
- All variables defined in an interface must be public, static, and final—in other words, interfaces can declare only constants, not instance variables.
- interfaces are implicitly abstract whether you type abstract or not.declarations are legal, and functionally identical:
    
    ```java
    public abstract interface Rollable { }
    public interface Rollable { }
    ```
    
- An interface can extend one or more other interfaces.
- An interface cannot extend anything but another interface.
- An interface cannot implement another interface or class.
- If we have a method in Interface with signature void doStuff(); we can’t implement the interface and have a method as void doStuff() in the subclass, as we see all the methods in interface are abstract and PUBLIC, but as soon as we implement it in class the void doStuff() in class has a DEFAULT access, hence the implementation has narrowed the access, and it will throw error.
    
    ```java
    Attempting to assign weaker privilege; was public
    ```
    
- For a subclass, if a member of its superclass is declared public, the subclass inherits that member regardless of whether both classes are in the same package.
- When a member is declared private, a subclass can't inherit it.
- You can, however, declare a matching method in the subclass. But regardless of how it looks, it is not an overriding method! It is simply a method that happens to have the same name as a private method (which you're not supposed to know about) in the superclass.
- The subclass can see the protected member only through inheritance. NO DOT OPERATOR.
- Once the subclass-outside-the-package inherits the protected member, that member (as inherited by the subclass) becomes private to any code outside the subclass,
i.e. if the SuperClass is A in package com.sumit.A and Subclass B is in package com.sumit.B
B can Access A’s member variable protected int x=0; only using inheritance that is B extends A. not like
    
    ```java
    public class B extends A{
        A a = new A();
        System.out.println(a.x); //This will not compile.
    }
    ```
    

	```java
	In Addition if a class  C under same package as B, com.sumit.B , will not be able to access “x” as in the class B the inherited variable x acts as private. So if C does

	public class C{ //same package as B
	B b = new B();
	System.out.println(b.x); //will throw exception.
	}
	```

- any local variable declared with an access modifier will not compile.
[https://javaranch.com/campfire/StoryCups.jsp](https://javaranch.com/campfire/StoryCups.jsp)

### Default Interface

Default Methods allowed java to have multiple Inheritance.
but if we have 2 interface with same method we will get compiler error,
if we haven't implemented it in child class.

*we can have static method in interfaces as well. This helps it becoming a utility
interface. AND BY DEFAULT YOU CAN'T OVERRIDE STATIC METHOD*

```java
interface I1 {
    default void m() {
        System.out.println("in I1");
    }
}

interface I2 {
    default void m() {
        System.out.print("in I2");
    }
    static void hello(){
        System.out.print("I am static method");
    }
}

public class HelloWorld implements I1, I2 {

    @Override
    public void m() {
        I1.super.m();
        System.out.print("in m()");
    }

    public static void main(String[] args) {
        HelloWorld h = new HelloWorld();
        h.m();
    }
}
// RETURNS in I1 in m();
```

---

## Contructor

- Constructors can't be marked static (they are after all associated with object instantiation), they can't be marked final or abstract (because they can't be overridden).
- local variable must be initialized before you try to use it. The compiler will reject any code that tries to use a local variable that hasn't been assigned a value, because—unlike instance variables—local variables don't get default values.
- If you mark an instance variable as `transient`, you're telling the JVM to skip (ignore) this variable when you attempt to serialize the object containing it.
- Attempting to access an instance variable from a static context (typically from main():
    
    ```java
    class ScopeErrors {
        int x = 5;
        public static void main(String[] args) {
        x++; // won't compile, x is an 'instance' variable
        //and main is a static function.
        }
    }
    ```
    


## Method Overriding

- In every case, when an exact match isn't found, the JVM uses the method with the smallest argument that is wider than the parameter.
the compiler will choose widening over boxing`Widening > Boxing > var-args`

*it's not legal to widen from one wrapper class to another, because the wrapper classes are peers to one another.*

```java
public class HelloWorld {

    public void m(int i){
        System.out.println("int");
    }

    public void m(float i){
        System.out.println("float");
    }
    public static void main(String[] args) throws Exception{
        HelloWorld h = new HelloWorld();
        byte b = 10;
        h.m(b);
    }
}
```

- You CANNOT widen and then box. (An int can't become a Long.)
- You can box and then widen. (An int can become an Object, via Integer.)
    
    ```java
    class WidenAndBox {
        static void go(Long x) {
            System.out.println("Long");
        }
    
        public static void main(String[] args) {
            byte b = 5;
            go(b); // must widen then box - illegal
        }
    }
    ```
    

ANOTHER EXAMPLE:

```java
public class HelloWorld {

    public void m(List i) {
        System.out.println("list");
    }

    public void m(Collection i) {
        System.out.println("collection");
    }

    public void m(ArrayList i) {
        System.out.println("array");
    }

    public static void main(String[] args) throws Exception {
        HelloWorld h = new HelloWorld();
        List<Integer> l = new ArrayList<Integer>();
        h.m(l);
    }
}
```

Widening primitive conversion (§5.1.2) is applied to convert either or both operands as specified by the following rules:

- If either operand is of type `double`, the other is converted to `double`.
- Otherwise, if either operand is of type `float`, the other is converted to `float`.
- Otherwise, if either operand is of type `long`, the other is converted to `long`.
- **Otherwise, both operands are converted to type `int`.**

**The left side defines the OVERLOADING and right side defines OVERRIDING.**

### OVERLOADING

Two methods will be treated as overloaded if both follow the mandatory rules below:

> Both must have the same method name.
Both must have different argument lists.
> 

And if both methods follow the above mandatory rules, then they may or may not:

> Have different return types.
Have different access modifiers.
Throw different checked or unchecked exceptions.

### OVERRIDING

- It must have the same method name.
- It must have the same arguments.
- It must have the same return type. From Java 5 onward, the return type can also be a subclass (subclasses are a covariant type to their parents).
- It must not have a more restrictive access modifier (if parent --> protected then child --> private is not allowed).
- It must not throw new or broader checked exceptions.

And if both overriding methods follow the above mandatory rules, then they:

- May have a less restrictive access modifier (if parent --> protected then child --> public is allowed).
- May throw fewer or narrower checked exceptions or any unchecked exception.

## Collections

```java
Collection cs = new ArrayList<String>(); //this will work fine.
Collection cs = new HashSet<String>();
```

But it won’t compile:
```
Collection cs =new HashMap<String, Integer>(); 
//as Hashmap is not a part of Collection.
```

### Synchronized vs Concurrent Collection

- Synchronized collections locks the whole collection
- Concurrent collection uses lock stripping. For example, the ConcurrentHashMap divides the whole map into several segments and locks only the relevant segments, which allows multiple threads to access other segments of same ConcurrentHashMap without locking. But if the Map grows longer its costly to lock the whole MAP. Instead it doesn’t matter to ConcurrentHashMap as it uses a part to lock.

- CopyOnWriteArrayList allows multiple reader threads to read without synchronization and when a write happens it copies the whole ArrayList and swap with a newer one.
- Synchronized ArrayList is a synchronized collection while CopyOnWriteArrayList is a concurrent collection.
CopyOnWriteArray is more scalable than Sync ArrayList.
- The Iterator returned from synchronized ArrayList is a fail fast but iterator returned by CopyOnWriteArrayList is a fail-safe iterator i.e. it will not throw ConcurrentModifcationException even when the list is modified
- *NOTE: the size of ArrayList if its big then obviously cost of copying after a write operation is high enough to compensate the cost of locking but if ArrayList is really tiny then you can still use CopyOnWriteArrayList.*

### Sorting a Hashmap by Values

```java
Map<String, String> hashMap = new HashMap<>();
//... add some random values.
Entry<String, String> entries = Map.entrySet();
Comparator<Entry<String, String>> valueComparator = new Comparator<Entry<String,String>>() {

      @Override
      public int compare(Entry<String, String> e1, Entry<String, String> e2) {
          String v1 = e1.getValue();
          String v2 = e2.getValue();
          return v1.compareTo(v2);
      }
  };

// Sort method needs a List, so let's first convert Set to List in Java
List<Entry<String, String>> listOfEntries = new ArrayList<>(entries);

// sorting HashMap by values using comparator
Collections.sort(listOfEntries, valueComparator);

LinkedHashMap<String, String> sortedByValue = new LinkedHashMap<>(listOfEntries.size());

// copying entries from List to Map
for(Entry<String, String> entry : listOfEntries){
    sortedByValue.put(entry.getKey(), entry.getValue());
}
```
### Fail Fast Iterator:

- Enhanced for loop is fail fast.

- Fail Fast Iterator: Means any structural modification made to ArrayList like `adding or removing elements` during Iteration will throw java.util.ConcurrentModificationException.
```java
Iterator<String> iterator=arrayList.iterator();
while(iterator.hasNext()){
	//iterator.next() should be called else we will face other errors.
    System.out.println(iterator.next());
        arrayList.add("ind"); //Exception in thread "main" java.util.ConcurrentModificationException
        arrayList.remove("var"); //Exception in thread "main" java.util.ConcurrentModificationException
        it.remove(); // no Exception.
        //iterator doesnt have an add() function.
}
```


### ArrayList

- java.util.ArrayList is Resizable-array implementation of the java.util.List interface. ArrayList `implements RandomAccess(Marker interface)` to indicate that they support fast random access (i.e. index based access) in java.
- Iterator returned by java.util.ArrayList is fail-fast. Means any structural modification made to ArrayList like `adding or removing elements` during Iteration will throw java.util.ConcurrentModificationException.

`Removing element by using iterator is allowed.`



Element has been added during iteration which will cause ConcurrentModificationException to be thrown.
`Enhanced for Loop is also Fail Fast.`

- Default initial capacity of ArrayList is 10. ArrayList is resized by 50% of it’s current size.
- Initially when a ArrayList is created its size is 0, As soon as first element is added, using add(i), where i=1, ArrayList is initialized to it’s default capacity of 10.

### LinkedList

- java.util.LinkedList is implemented using Doubly-linked list.
- `Get method iterates on nodes sequentially to get element on specified index. Hence, offering O(n) complexity.`
- Iterator returned by LinkedList is fail fast in java.

### HashSet

- java.util.HashSet is implementation of the java.util.Set interface.

- HashSet enables us to store element, it does not allow us to store duplicate elements in java.
- `iterator returned by java.util.HashSet is fail-fast.`
- HashSet is internally implemented using java.util.HashMap in java.

```java
// Dummy value to associate with an Object in the backing Map
private static final Object PRESENT = new Object();

/**
 * Constructs a new, empty set; the backing <tt>HashMap</tt> instance has
 * default initial capacity (16) and load factor (0.75).
 */
public HashSet() {
    map = new HashMap<>();
}
/**
 * Adds the specified element to this set if it is not already present.
 * More formally, adds the specified element <tt>e</tt> to this set if
 * this set contains no element <tt>e2</tt> such that
 * <tt>(e==null&nbsp;?&nbsp;e2==null&nbsp;:&nbsp;e.equals(e2))</tt>.
 * If this set already contains the element, the call leaves the set
 * unchanged and returns <tt>false</tt>.
 *
 * @param e element to be added to this set
 * @return <tt>true</tt> if this set did not already contain the specified
 * element
 */
public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}
		
```

- Null elements - One null element can be added in HashSet in java.
- What is Load Factor in java: Default load factor is 0.75
That means when set will be 75% filled, it’s capacity will be doubled.

### HashMap

- java.util.HashMap is implementation of the java.util.Map interface.
- HashMap enables us to store data in key-value pair form.
- iterator returned is fail fast in java.
- `The HashCode will define the bucket in which a Element goes and Equal defines if the Element gets overridden or not`
- `TreeMap does not allow to store null key but allow many null values.`
- LinkedHashMap uses doubly linked lists and TreeMap uses Red black tree.
- `Hashing` means generating a (hopefully) unique number that represents a value.
    - Computing a hash is not necessarily constant-time with regard to the value being hashed.
    For example, computing the hash of a string involves iterating the string, and isn't constant-time with regard to the size of the string.
-  **JAVA 8 HASHMAP IMPROVEMENTS**
    - TREEIFY_THRESHOLD = 8
    - Buckets containing a large number of colliding keys will store their entries in a balanced tree instead of a linked list after certain threshold is reached.
    - This tree is a Red-Black tree. It is first sorted by hash code. If the hash codes are the same, it uses the compareTo method of Comparable 
    - Red-black tree is balanced.
    - If entries are removed from the map, the number of entries in the bucket might reduce such that this tree structure is no longer necessary. That's what the UNTREEIFY_THRESHOLD = 6 is for. If the number of elements in a bucket drops below six, we might as well go back to using a linked list.
    
    - While converting the list to binary tree, hashcode is used as a branching variable. If there are two different hashcodes in the same bucket, one is considered bigger and goes to the right of the tree and other one to the left. But when both the hashcodes are equal, HashMap assumes that the keys are comparable, and compares the key to determine the direction so that some order can be maintained.

### HashCode and Equals

1. if hasCode is returning 1 and equals is returning true,
   The elements goes into the same bucket. Already existing entries will get overridden.
2. If hashCode is returning 1 and equals is returning false,
   it gets into the same bucket, but because of equals it doesn't override.
3. if hashCode is returning computed value for each element,
   it goes into different buckets so even if you do equals true, only objects with same reference will get overriden. 

## EXCEPTION

Checked exceptions are checked at compile-time. It means if a method is throwing a checked exception then it should handle the exception using try-catch block or it should declare the exception using throws keyword, otherwise the program will give a compilation error.
example: `SQLException, IOExeception, ClassNotFoundException`

Unchecked exceptions are not checked at compile time. It means if your program is throwing an unchecked exception and even if you didn’t handle/declare that exception, the program won’t give a compilation error.
example: `NullPointerException, ArrayIndexOutOfBoundException, IllegalArgumentException, NumberFormtException, ArithematicException`

NumberFormtException

```java
String s = "FOOBAR";
int i = Integer.parseInt(s)
```
### 3 ways to create objects of a class 'Class'

- By using` Class.forName()`:

		Class c1 = Class.forName("Class1");
- By using `getClass()` :

		Class1 c = new Class();
		Class c1 = c.getClass();
- By using `.class` :

		Class c1 = Class1.class;

## Serialization:

- When we Deserialize class ( class which has been modified after Serialization and also class declare SerialVersionUID) its gets DeSerialized successfully.
- When we Deserialize class ( class which has been modified after Serialization and also class doesn’t declare SerialVersionUID) InvalidClassException is thrown.

Serialization allows Compatible and Incompatible Changes:

- compatible changes:
    - adding new fields.
    - Changing access modifier of a field
    - changing a field from static to non static (similar to adding new field).
- incompatible changes:
    - removing fields.
    - changing from nonstatic to static fields (similar to deleting old fields).

> If Serialization is not present we can use JSON to transmit over network and Hibernate to persist in DB.
> 
- ArrayList, HashSet and HashMap implements Serializable interface, so if we will use them as member of class they will get Serialized and DeSerialized as well
- If Serializable has been implemented - constructor is not called during DeSerialization process.
- If superclass has implemented Serializable - constructor is not called during DeSerialization process.
- If superclass has not implemented Serializable - constructor is called during DeSerialization process.
- Objects and primitives would be initialized the default value `when deserialized` if they were not part of serialization.
- If superClass has implemented Serializable that means subclass is also Serializable (as subclass always inherits all features from its parent class), `for avoiding Serialization in sub-class we can define writeObject() method and throw NotSerializableException()` from there as done below.
    
    ```java
    private void writeObject(ObjectOutputStream os) throws NotSerializableException {
        throw new NotSerializableException("This class cannot be Serialized");
    }
    ```
    

# Immutable Class:

An immutable object is one that will not change state after it is instantiated.

In general, an immutable object can be made by defining a class which does not have any of its members exposed, and does not have any setters.

```java
class ImmutableInt {
    private final int value;
    private final List<T> list;

    public ImmutableInt(int i, List<T> list) {
        // creates a new ArrayList and keeps a reference to it.
        this.list = new ArrayList(list);
        value = i;
    }

    public int getValue() {
        return value;
    }

    public List<T> getList() {
        // return a copy of the list so the internal state cannot be altered
        return new ArrayList(list);
		}
}
```

Reflection breaks immutability since we can use it to change non-constant final fields.

An example of a Reflection-resistant immutable object would be the following:

```java
static class Immutable {
    // This field is a constant, and cannot be changed using Reflection
    private final int value = Integer.MIN_VALUE;

    public int getValue() {
        return value;
    }
}

public static void main(String[] args) throws NoSuchFieldException, SecurityException, IllegalArgumentException, IllegalAccessException {
    final Immutable immutable = new Immutable();

    final Field f = Immutable.class.getDeclaredField("value");
    f.setAccessible(true);

    System.out.println(immutable.getValue());
    f.set(immutable, Integer.MAX_VALUE);
    System.out.println(immutable.getValue());
}
```

This can’t be broken. 

Even with Serialization we can break immutability if someone has access to the byte stream they can alter the bytes and they break immutability.


### Differences between Interface and Abstract class:

|  Interface | Abstract Class |
|--|--|
| Interfaces dont extend classes | Abstract classes extends one or more Interfaces  |
|Interfaces dont have constructors | Classes have constructors |
| All functions in interfaces need to be implemented by the subclasses | Only Abstract functions needs to be implemented. |
|Interfaces dont have synchronized functions. | Instance menthods in Abstract class can be synchronized. But **Abstract Functions are not synchronized.** |
|All the variables in Interface are public, [static](http://www.javamadesoeasy.com/2015/05/static-keyword-in-java-variable-method.html) and [final](http://www.javamadesoeasy.com/2015/05/final-keyword-in-java-20-salient.html) by default. Interface variables are also known as constants. | Abstract class can have private, instance variables as well.



[JAVA 8](https://www.notion.so/JAVA-8-3c7b3b54902f481da4b6d484734db240?pvs=21)


### Runnable vs. Callable
The Runnable interface is very similar to the Callable interface. The Runnable interface represents a task that can be executed concurrently by a thread or an ExecutorService. The Callable can only be executed by an ExecutorService. Both interfaces only has a single method. There is one small difference between the Callable and Runnable interface though. The difference between the Runnable and Callable interface is more easily visible when you see the interface declarations.

Here is first the Runnable interface declaration:

public interface Runnable {
    public void run();
}
And here is the Callable interface declaration:

public interface Callable{
    public Object call() throws Exception;
}
The main difference between the Runnable run() method and the Callable call() method is that the call() method can return an Object from the method call. Another difference between call() and run() is that call() can throw an exception, whereas run() cannot (except for unchecked exceptions - subclasses of RuntimeException).

If you need to submit a task to a Java ExecutorService and you need a result from the task, then you need to make your task implement the Callable interface. Otherwise your task can just implement the Runnable interface.


---

- When try and finally block both return value, method will ultimately return value returned by finally block irrespective of value returned by try block.
- Fail Safe Iterator : Fail Safe Iterator makes copy of the internal data structure (object array) and iterates over the copied data structure.  
	Any structural modification done to the iterator affects the copied data structure.
- MIN_TREEIFY_CAPACITY is the minimum number of buckets before a certain bucket is transformed into a Tree
- regex - The Java Regex or Regular Expression is an API to define a pattern for searching or manipulating strings.
- Why closing connection in try block may not be safe?  
	Because exception may be thrown in try block before reaching connection closing statement.
- Why closing connection in catch block may not be safe?  
	Because inappropriate exception may be thrown in try block and we might not enter catch block to close connection.
- Error: Stackoverflow  
  Checked Exception : FileNotFound, SQLException, IOException, ClassNotFound  
  Unchecked Exception : ArithmeticException, NullPointerException, ArrayIndexOutOfBoundsException  
- The class Exception and all its subclasses that are not also subclasses of RuntimeException are checked exceptions.
- The class RuntimeException and all its subclasses are unchecked exceptions. The class Error and all its subclasses are unchecked exceptions.
- If you handle unchecked exception then program is not terminated. No need to handle unchecked exception by catching it or throwing it.
- Checked Exceptions need throw and throws keyword. Checked Exception is propagated to JVM using throws keyword.
- finally fails when System.exit or JVM crashes: Error occurs
- Object - Throwable - Error - OutOfMemory, Stackoverflow, IOError
					 - Exception - Checked
```								 - Unchecked
- java.lang.Object  
  |_  java.lang.Throwable  
    |_    java.lang.Exception  
       |_     java.io.IOException  
  	  |_     java.io.FileNotFoundException 
``` 
- Daemon thread invokes finalize method. Daemon threads are low priority threads which runs intermittently in background for doing garbage collection. 
- System.gc();  
  Runtime.getRuntime().gc();  
  By calling these methods JVM runs the finalize() methods of any objects.  Calling these methods does not guarantee that it will immediately start performing garbage collection.  
- Class must implement interface java.lang.Cloneable for calling clone method to the object or else it throws CloneNotSupportedException.  
	shallow copy- If we implement Cloneable interface, we must override clone method and call super.clone() from it, invoking super.clone() will do shallow copy.  

		@Override
		public Object clone() {
			   System.out.println("Doing shallow copy");
			   try {
					  return super.clone();
			   } catch (CloneNotSupportedException e) {
					  return null;
			   }
		}

	Deep copy - We need to provide custom implementation of clone method for deep copying.  When the copied object contains some other object its references are copied recursively in deep copy. When you implement deep copy be careful as you might fall for cyclic dependencies

			@Override
			public CloneDeep clone(){ 		//CloneDeep is the class name
				   System.out.println("Doing deep copy");				   
				   Map<Integer,Integer> map=new HashMap<Integer,Integer>();
				   Iterator<Integer> it=this.map.keySet().iterator();
				   while(it.hasNext()){
						  Integer key=it.next();
						  map.put(key,this.map.get(key) );
				   }				   
				   CloneDeep cloneDetailedDeep=new CloneDeep(new String(name), map);				   
				   return cloneDetailedDeep;
			}
  
- In shallow copy, different object is created after cloning (i.e. clonedEmp is created from emp) but member variables keep on referring to same object (i.e. name and map).
- In deep copy, different object is created after cloning (i.e. clonedEmp is created from emp) , also member variables starts referring to different objects (i.e. name and map).
- In shallow copy, only fields of primitive data type are copied while the objects references are not copied. Deep copy involves the copy of primitive data type as well as object 
	references. Lazy copy is a combination of both of these approaches.
- Overriding in Java means that the method would be called on the run time based on type of the object and not on the compile time type of it . But static methods are class methods access to them is always resolved during compile time only using the compile time type information. Thats why static methods cannot be ovverriden.

- StringBuffer is Synchronised where as StringBuilder is not Synchronised. And both are mutable and are created in Java heap.
- In java when any changes are made to the argument in passed method  the value of original argument is not changed. Hence, Java is call by value.  
	In java when any changes are made to the argument in passed method  the value of original argument is not changed. Hence, Java is NOT call by reference.  
	In Call by reference, reference of argument is passed to method. Call by value means Copy of an argument is passed to method. 
- Inner classes cannot not declare static initialization blocks. Inner classes cannot not declare member interfaces. Inner classes cannot not declare static members  
	Top level class is always non static in java.  
	Accessing method of inner class : new OuterClass().new InnerClass().method();  
	Static and non-static member variables of outer class can be accessed inside methods of non-static class  
- Static nested classes cannot be declared in abstract class/interface.  
	Accessing static nested class : new OuterClass.StaticNestedClass().method();  
	Only Static member variables of outer class can be accessed inside methods of static nested class.  
	Top level class can never be static in java.  
- AnonymousInnerClass means class with no name.
  ```
	interface MyInterface{
		void m();
	}
	public class InnerClassTest {
		public static void main(String[] args) {			   
			   //Anonymous inner class
			   new MyInterface(){ //implementing interface
					  public void m(){ //Provide implementation of MyInterface's m() method
							System.out.println("implementation of MyInterface's m() method");
					  }
			   }.m();         
			}			
		}/* OUTPUT : implementation of MyInterface's m() method */
  ```
- Static blocks executes when class is loaded in java. this keyword cannot be used in static blocks.  
	Instance block executes only when instance of class is created, not called when class is loaded in java. this keyword can be used in instance block.
- Serialization is process of converting object into byte stream. Once, object have have been transferred over network or persisted in file or in database, we could deserialize the object and retain its state as it is in which it was serialized.
- Static or Transient : Used to avoid  certain member variables of class from getting Serialized.
- serialVersionUID is used for version control of object.  
	If we  don’t define serialVersionUID in the class, and any modification is made in class, then we won’t be able to deSerialize our class because serialVersionUID generated by java compiler for modified class will be different from old serialized object. And deserialization process will end up throwing java.io.InvalidClassException.  
	The serialization at runtime associates with each serializable class a version number, called a serialVersionUID, which is used during deserialization to verify that the sender and receiver of a serialized object have loaded classes for that object that are compatible with respect to serialization. 

- Classloaders are used to dynamically load class at runtime in JVM
	- Bootstrap Classloader - It loads JDK internal classes, typically loads rt.jar and other core classes for example java.lang.* package classes
	- Extension Classloader - It loads classes from the JDK extensions directory, usually $JAVA_HOME/lib/ext directory.
	- System Classloader - It loads classes from the current classpath that can be set while invoking a program using -cp or -classpath command line options.


## Status Code:
| Backend Status | Gateway Mapping Behavior            | Browser Sees     |
|----------------|--------------------------------------|------------------|
| 200            | Forwarded as-is                      | 200              |
| 400            | Forwarded as-is                      | 400              |
| 500            | Forwarded or transformed             | 500 (or custom)  |
| 499            | Treated as timeout or error          | 502 / 504 / 500  |
| Unknown/Invalid| Default error response               | 500              |

499 from NGINX typically becomes a 502 at the gateway level.

Example: Map all 5xx backend errors to a custom 503 with a friendly JSON message.

