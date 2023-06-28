# Builder:

### The Builder pattern
Builder is a creational design pattern that lets you construct complex objects step by step.

### Intent
Each method return the object reference it`s called from.

There is build() method which returns a fully contstructed object (based on all the intermediate calls).

#### JAVA EXAMPLE:
-------------
`StringBuilder.append();`{:.java}

-------------

### Example:
you want to create a house.Now in order to create a house you need 4 walls, celling, windows, door, etc.
Its like a complex object creation. and furthermore if you want to add extra features like-> swimming pool, garden, backyard etc you would need more parameters/complexity.

1. one way to solve this is to create a `house` Superclass and can have multiple subclasses with features like:  `houseWithGarden` or  `houseWithGarage` etc. But this would be complicated for each new requirement you would have to create a new SubClass.
2. The second approach is to have as many parameters in the `house` constructor as you can example:
      `public House(windows, door, lawn....)`{:.java} but the constructor call would be ugly:
      `new House(4,3,5, null, null, null....)`{:.java}


To avoid this we use Builder pattern:
Example:
```
House house = house
          .buildWalls()
          .buildWindows()
          .buildLawn()
          .getResult();
```


The pattern organizes object construction into a set of steps (buildWalls, buildDoor, etc.). To create an object, you execute a series of these steps on a builder object. 



### Application:
to Avoid Telescope Constructors:
```
class Pizza {
    Pizza(int size) { ... }
    Pizza(int size, boolean cheese) { ... }
    Pizza(int size, boolean cheese, boolean pepperoni) { ... }
    // ...
```

### Cons:
The overall complexity of the code increases since the pattern requires creating multiple new classes.

## Code Example:
#### Problem Solution
Improves the readability of the code when object creation has many parameters.
Useful when some or all paramaters are optional.
Structure of the Builder Pattern
Firstly, you need to define a base class with all args constructor. Then, you have to create a Builder class with attributes and setters for each argument of the base class. Each setter should return the builder. Then, need to create a build() methid that will construct and return object of base class.

```
public class Employee{
    private String name;
    private String address;
    
    //getters
}

```
```
public class EmployeeBuilder{
    private String name;
    private String address;
    
    //setters which returns the Builder Only:
    public EmployeeBuilder(string name){
          this.name =name;
          return this;
    }
    //constructor which calls the Employee Constructor:
    public EmployeeBuilder(){
          new Employee(name, address);
    }
}

```
And this is how you return a new Employee 

```
public Employee getEmployee2() {  
        return new EmployeeBuilder()  
                .setEmployeeId("4321")  
                .setAddress("Jeff")    
                .buildEmployee();  
    }  

```
